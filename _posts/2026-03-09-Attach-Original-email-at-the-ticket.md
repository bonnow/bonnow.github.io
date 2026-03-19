---
layout: post
title: "Attach original email at the Ticket"
date: 2026-03-09 10:00:00 +0800
categories: [servicenow platform]
tags: [inbound, email, attachment, original]
---
Created with the Business rule.

<img width="1447" height="992" alt="image" src="https://github.com/user-attachments/assets/3e50fd07-a6f0-4ebc-869c-f086cebe06f6" />

```javascript
(function executeRule(current /* sys_email */ , previous /* null */ ) {
    var targetTable = (current.target_table + '').trim();
    var targetSysId = (current.instance + '').trim();

    if (!targetTable || !targetSysId) gs.info('stop');

    var gr = new GlideRecord(targetTable);
    if (!gr.get(targetSysId)) gs.info('stop');

    var eml = buildEML(current);
    var fname = (current.getValue('subject') || 'email') + '.eml';

    if (!hasExistingAttachment(targetTable, targetSysId, fname)) {
        new GlideSysAttachment().write(gr, fname, 'message/rfc822', eml);
    }

    // 원본 첨부(파일형)도 필요하면 같이 복사
    GlideSysAttachment.copy('sys_email', current.sys_id, targetTable, targetSysId);

})(current, previous);

function resolveUserToEmail(userVal) {
    if (!userVal) return '';
    if (userVal.indexOf('@') > -1) return userVal;

    var isSysId = /^[0-9a-f]{32}$/i.test(userVal);
    var su = new GlideRecord('sys_user');

    if (isSysId) {
        if (su.get(userVal)) return su.getValue('email') || '';
    } else {
        su.addQuery('email', userVal).addOrCondition('user_name', userVal).addOrCondition('name', userVal);
        su.setLimit(1);
        su.query();
        if (su.next()) return su.getValue('email') || '';
    }
    return '';
}

function wrap76(s) {
    // base64 줄바꿈(76 chars) – 대부분의 MUA 호환
    var out = [];
    for (var i = 0; i < s.length; i += 76) out.push(s.substring(i, i + 76));
    return out.join('\r\n');
}

function getInlineImages(emailSysId) {
    // sys_email의 첨부 중 image/* 만 인라인 후보로
    var imgs = [];
    var att = new GlideRecord('sys_attachment');
    att.addQuery('table_name', 'sys_email');
    att.addQuery('table_sys_id', emailSysId);
    att.addQuery('content_type', 'STARTSWITH', 'image/');
    att.query();
    while (att.next()) {
        var bytes = new GlideSysAttachment().getBytes(att.sys_id + '');
        var b64 = GlideStringUtil.base64Encode(bytes);
        imgs.push({
            sys_id: att.sys_id + '',
            name: att.file_name + '',
            ctype: att.content_type + '',
            b64: b64
        });
    }
    return imgs;
}

function rewriteHtmlSrcToCid(html, inlineImgs) {
    // /sys_attachment.do?sys_id=<id> → cid:<id>@sn
    // 기록하면서 실제로 존재하는 sys_id만 cid로 교체
    var ids = {};
    inlineImgs.forEach(function(x) {
        ids[x.sys_id] = true;
    });

    return (html || '').replace(/(["'])\/sys_attachment\.do\?sys_id=([0-9a-f]{32})\1/ig, function(m, quote, id) {
        if (ids[id]) return quote + 'cid:' + id + '@sn' + quote;
        return m;
    });
}

function buildEML(emailGr) {
    var html = emailGr.getValue('body') || emailGr.getValue('body_text') || '';
    var fromAddr = resolveUserToEmail(emailGr.getValue('user')) ||
        emailGr.getValue('sys_from') || '';
    var toAddr = emailGr.getValue('recipients') || emailGr.getValue('direct') || '';
    var ccAddr = emailGr.getValue('copied') || '';
    var bccAddr = emailGr.getValue('blind_copied') || '';

    // 인라인 이미지 수집 및 cid 치환
    var inlineImgs = getInlineImages(emailGr.getUniqueValue());
    var htmlWithCid = rewriteHtmlSrcToCid(html, inlineImgs);

    var CRLF = '\r\n';
    var boundary = '====SN_REL_' + gs.generateGUID().replace(/-/g, '');

    // 헤더(related)
    var hdr =
        'From: ' + fromAddr + CRLF +
        'To: ' + toAddr + CRLF +
        (ccAddr ? 'Cc: ' + ccAddr + CRLF : '') +
        (bccAddr ? 'Bcc: ' + bccAddr + CRLF : '') +
        'Subject: ' + (emailGr.getValue('subject') || '') + CRLF +
        'Date: ' + (emailGr.getValue('sys_created_on') || '') + CRLF +
        'MIME-Version: 1.0' + CRLF +
        'Content-Type: multipart/related; boundary="' + boundary + '"' + CRLF + CRLF;

    // 본문 파트 (text/html)
    var body =
        '--' + boundary + CRLF +
        'Content-Type: text/html; charset=UTF-8' + CRLF +
        'Content-Transfer-Encoding: 8bit' + CRLF + CRLF +
        htmlWithCid + CRLF;

    // 인라인 이미지 파트들
    inlineImgs.forEach(function(img) {
        body +=
            '--' + boundary + CRLF +
            'Content-Type: ' + img.ctype + '; name="' + img.name + '"' + CRLF +
            'Content-Transfer-Encoding: base64' + CRLF +
            'Content-ID: <' + img.sys_id + '@sn>' + CRLF +
            'Content-Disposition: inline; filename="' + img.name + '"' + CRLF + CRLF +
            wrap76(img.b64) + CRLF;
    });

    // 종료 바운더리
    body += '--' + boundary + '--' + CRLF;

    return hdr + body;
}

function hasExistingAttachment(table, sysId, fileName) {
    var att = new GlideRecord('sys_attachment');
    att.addQuery('table_name', table);
    att.addQuery('table_sys_id', sysId);
    att.addQuery('file_name', fileName);
    att.setLimit(1);
    att.query();
    return att.next();
}
```

However this email attachment doesn’t show the image or the attachments which included in the original email.
