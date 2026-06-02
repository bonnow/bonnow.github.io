---
layout: post
title: "Service Request Framework etc"
date: 2026-05-01 10:00:00 +0800
categories: [servicenow itsm]
tags: [request, workflow, flow, service]
---

<style>
  .lang-switcher {
    display: flex;
    justify-content: flex-end;
    gap: 10px;
    margin-bottom: 30px;
    padding: 10px 0;
    border-bottom: 1px solid var(--main-border-color);
  }

  .lang-btn {
    padding: 8px 16px;
    border-radius: 999px;
    border: 1px solid var(--button-border-color);
    background-color: var(--card-bg);
    color: var(--text-color);
    font-size: 0.9rem;
    font-weight: 600;
    cursor: pointer;
    outline: none;
    transition: all 0.2s ease;
  }

  .lang-btn:hover {
    border-color: var(--link-color);
    transform: translateY(-1px);
  }

  .lang-btn.active {
    background-color: var(--link-color);
    color: #fff;
    border-color: var(--link-color);
  }

  .lang-ko {
    display: none;
  }

  .lang-note {
    color: #888;
    font-size: 0.9em;
  }
</style>

<div class="lang-switcher">
  <button class="lang-btn active" id="btn-en" onclick="switchLang('en')">🌐 English</button>
  <button class="lang-btn" id="btn-ko" onclick="switchLang('ko')">🇰🇷 한국어</button>
</div>

<script>
  function switchLang(lang) {
    const enElements = document.querySelectorAll('.lang-en');
    const koElements = document.querySelectorAll('.lang-ko');
    const btnEn = document.getElementById('btn-en');
    const btnKo = document.getElementById('btn-ko');

    if (lang === 'ko') {
      enElements.forEach(function(el) {
        el.style.display = 'none';
      });
      koElements.forEach(function(el) {
        el.style.display = 'block';
      });
      btnEn.classList.remove('active');
      btnKo.classList.add('active');
    } else {
      enElements.forEach(function(el) {
        el.style.display = 'block';
      });
      koElements.forEach(function(el) {
        el.style.display = 'none';
      });
      btnKo.classList.remove('active');
      btnEn.classList.add('active');
    }
  }
</script>

<div class="lang-en" markdown="1">
*The extra configurations that compose the Service Request Framework*


## Show the Request order

![image](https://github.com/user-attachments/assets/14e03f44-ecb5-4149-8c2b-32f64e60bd1d#.png)

If you want to show the request order for eacfh Requested Item catalogue like this one.

<img width="1452" height="745" alt="image" src="https://github.com/user-attachments/assets/0b75a31f-6bc5-4c8b-9c81-52da6f4a22ff" />

<img width="1449" height="649" alt="image" src="https://github.com/user-attachments/assets/9373742c-bb25-4a24-89bf-a7a3042535e5" />

```javascript
(function executeRule(current, previous) {
    var str = '<style>.sr_at_notice_block {border-collapse: collapse;border: 1px solid black;}.sr_at_notice {border: 1px solid black;}</style><div style="text-align: center;"><table class="sr_at_notice_block" width="500px"><colgroup><col width="10%"/><col width="15%"/><col width="45%"/><col width="30%"/></colgroup><tr><th class="sr_at_notice">Order</th><th class="sr_at_notice">Type</th><th class="sr_at_notice">Title</th><th class="sr_at_notice">Assigned</th></tr>';

    var grAtLines = new GlideRecord('x_hap_sr_flow_sr_at_lines');
    grAtLines.addEncodedQuery('active=true^catalog_item=' + current.cat_item + '^ORDERBYorder');
    grAtLines.query();

    while (grAtLines.next()) {
        var conditionFail = false;
        var scriptedFail = false;

        if (grAtLines.condition) {
            var grChk = new GlideAggregate('sc_req_item');
            grChk.addEncodedQuery('sys_id=' + current.getUniqueValue() + '^' + grAtLines.condition);
            grChk.query();
            if (!grChk.next()) {
                conditionFail = true;
            }
        }

        if (grAtLines.scripted_condition) {
            var evaluator = new GlideScopedEvaluator();
            evaluator.putVariable('source', current);
            evaluator.putVariable('answer', null);
            evaluator.evaluateScript(grAtLines, 'scripted_condition', null);
            var result = evaluator.getVariable('answer');
            if (result !== true && result !== 'true') {
                scriptedFail = true;
            }
        }

        if (conditionFail || scriptedFail) {
            continue; // skip row
        }

        str += '<tr>';
        str += '<td class="sr_at_notice">' + grAtLines.order.getDisplayValue() + '</td>';
        str += '<td class="sr_at_notice">' + grAtLines.type.getDisplayValue() + '</td>';
        str += '<td class="sr_at_notice">' + grAtLines.title.getDisplayValue() + '</td>';
        if (grAtLines.groups) {
            str += '<td class="sr_at_notice">' + grAtLines.groups.getDisplayValue() + '</td>';
        } else if (grAtLines.variables) {
            str += '<td class="sr_at_notice">' + grAtLines.variables.getDisplayValue() + '</td>';
        } else {
            str += '<td class="sr_at_notice">' + grAtLines.fields.getDisplayValue() + '</td>';
        }
        str += '</tr>';
    }

    str += '</table></div>';
    current.work_notes = '[code]' + str + '[/code]';
    current.update();
})(current, previous);
```




## Event to send the email

Create the event name 'send_email' to use send email with the event type record.

![image](https://github.com/user-attachments/assets/aa4b12d7-c48e-4b24-ab90-15e59043a95d#.png)





</div>

<div class="lang-ko" markdown="1">
*The extra configurations that compose the Service Request Framework*

## Show the Request order

![image](https://github.com/user-attachments/assets/14e03f44-ecb5-4149-8c2b-32f64e60bd1d#.png)

If you want to show the request order for eacfh Requested Item catalogue like this one.

<img width="1452" height="745" alt="image" src="https://github.com/user-attachments/assets/0b75a31f-6bc5-4c8b-9c81-52da6f4a22ff" />

<img width="1449" height="649" alt="image" src="https://github.com/user-attachments/assets/9373742c-bb25-4a24-89bf-a7a3042535e5" />

```javascript
(function executeRule(current, previous) {
    var str = '<style>.sr_at_notice_block {border-collapse: collapse;border: 1px solid black;}.sr_at_notice {border: 1px solid black;}</style><div style="text-align: center;"><table class="sr_at_notice_block" width="500px"><colgroup><col width="10%"/><col width="15%"/><col width="45%"/><col width="30%"/></colgroup><tr><th class="sr_at_notice">Order</th><th class="sr_at_notice">Type</th><th class="sr_at_notice">Title</th><th class="sr_at_notice">Assigned</th></tr>';

    var grAtLines = new GlideRecord('x_hap_sr_flow_sr_at_lines');
    grAtLines.addEncodedQuery('active=true^catalog_item=' + current.cat_item + '^ORDERBYorder');
    grAtLines.query();

    while (grAtLines.next()) {
        var conditionFail = false;
        var scriptedFail = false;

        if (grAtLines.condition) {
            var grChk = new GlideAggregate('sc_req_item');
            grChk.addEncodedQuery('sys_id=' + current.getUniqueValue() + '^' + grAtLines.condition);
            grChk.query();
            if (!grChk.next()) {
                conditionFail = true;
            }
        }

        if (grAtLines.scripted_condition) {
            var evaluator = new GlideScopedEvaluator();
            evaluator.putVariable('source', current);
            evaluator.putVariable('answer', null);
            evaluator.evaluateScript(grAtLines, 'scripted_condition', null);
            var result = evaluator.getVariable('answer');
            if (result !== true && result !== 'true') {
                scriptedFail = true;
            }
        }

        if (conditionFail || scriptedFail) {
            continue; // skip row
        }

        str += '<tr>';
        str += '<td class="sr_at_notice">' + grAtLines.order.getDisplayValue() + '</td>';
        str += '<td class="sr_at_notice">' + grAtLines.type.getDisplayValue() + '</td>';
        str += '<td class="sr_at_notice">' + grAtLines.title.getDisplayValue() + '</td>';
        if (grAtLines.groups) {
            str += '<td class="sr_at_notice">' + grAtLines.groups.getDisplayValue() + '</td>';
        } else if (grAtLines.variables) {
            str += '<td class="sr_at_notice">' + grAtLines.variables.getDisplayValue() + '</td>';
        } else {
            str += '<td class="sr_at_notice">' + grAtLines.fields.getDisplayValue() + '</td>';
        }
        str += '</tr>';
    }

    str += '</table></div>';
    current.work_notes = '[code]' + str + '[/code]';
    current.update();
})(current, previous);
```


## Event to send the email

Create the event name 'send_email' to use send email with the event type record.

![image](https://github.com/user-attachments/assets/aa4b12d7-c48e-4b24-ab90-15e59043a95d#.png)

</div>
