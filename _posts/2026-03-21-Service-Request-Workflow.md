---
layout: post
title: "Service Request Workflow"
date: 2026-03-21 10:00:00 +0800
categories: [servicenow itsm]
tags: [request, workflow, flow, framework, service, request, framework]
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
Details of the SR workflow.


![image](https://github.com/user-attachments/assets/f8d778ca-e45f-407c-8c6e-f693a384e91a#.png)

## Properties

![image](https://github.com/user-attachments/assets/5197799b-77e2-43f4-a0ad-e80b613c2cc5#.png)

![image](https://github.com/user-attachments/assets/fa087e22-33d4-421b-89ea-ca8dbd93a519#.png)

![image](https://github.com/user-attachments/assets/8be28be2-1781-44e4-944d-cec8920e2b11#.png)

![image](https://github.com/user-attachments/assets/11f6817b-a3bd-40cf-9216-79e90a42cc7c#.png)

![image](https://github.com/user-attachments/assets/07ac4438-6852-407f-87bc-421546ce2b87#.png)

![image](https://github.com/user-attachments/assets/b8e5bc12-2a31-4808-b3cf-fd98beda50ec#.png)

![image](https://github.com/user-attachments/assets/eb2e6c80-1318-4420-aa1d-f4aefc9b74f9#.png)


## 1. Check the SR AT Lines Records

![image](https://github.com/user-attachments/assets/7e75ef71-f3b1-4aed-8e26-b4a43364403c#.png)

```javascript
(function(current, workflow) {

	workflow.scratchpad.srAtLines = [];
	workflow.scratchpad.current = [];
	workflow.scratchpad.now = {
		'catalog_item':'',
		'short_desc':'',
		'desc':'',
		'event':'',
		'fields':'',
		'groups':'',
		'title':'',
		'type':'',
		'variables':'',
		'assign_user':'',
		'assign_group':'',
		'business_service':'',
		'service_offering':'',
		'due_date':'',
		'primary_worker':'',
		'duplicate_approver':''
	};
	workflow.scratchpad.pre_line = [];

	var gr = new GlideRecord('x_hap_sr_flow_sr_at_lines');
	gr.addEncodedQuery('active=true^catalog_item='+current.cat_item.getValue()+'^ORDERBYorder');
	gr.query();

	while(gr.next()){
		workflow.scratchpad.srAtLines.push({
			'sysId' : gr.getUniqueValue(),
			'order' : gr.getValue('order'),
			'type' : gr.getValue('type')
		});
	}
	
})(current, workflow);
```

## 2. Check there are any lines in the same order

![image](https://github.com/user-attachments/assets/8bdf493e-8d44-498b-af68-97b0275b2b29#.png)

```javascript
(function(current, workflow) {
    var first = workflow.scratchpad.srAtLines.shift();
    workflow.scratchpad.current.push(first);

    var sameOrder = 0;

    for (var i = 0; i < workflow.scratchpad.srAtLines.length; i++) {
        if (workflow.scratchpad.srAtLines[i].order == first.order) {
            sameOrder = sameOrder + 1;
        } else {
            break;
        }
    }
    for (var j = sameOrder; j > 0; j--) {
        var tmp = workflow.scratchpad.srAtLines.shift();
        workflow.scratchpad.current.push(tmp);
    }
	
})(current, workflow);
```

## 3. Assgined to field is empty

![image](https://github.com/user-attachments/assets/72652cec-1e43-4c68-b537-ffc9a0a56a0f#.png)


## 4. Set State in WIP

![image](https://github.com/user-attachments/assets/098a56e7-3661-4342-af74-423e2e6d97f9#.png)


## 5. Set State to Open

![image](https://github.com/user-attachments/assets/21b2299c-4695-4a3e-ac65-f7d70d6da84a#.png)


## 6. Check the condition

![image](https://github.com/user-attachments/assets/c57db34b-bafc-4da4-8ff5-b46a89503825#.png)

```javascript
function checkCondition(current, workflow) {
	var ans = 'yes';
	var record = workflow.scratchpad.current[0];

	var gr = new GlideRecord('x_hap_sr_flow_sr_at_lines');
	gr.get(record.sysId);

	var conditionFail = false;
	var scriptedFail = false;

	// 1. Evaluate condition if present
	if (gr.condition) {
		var grRitm = new GlideAggregate('sc_req_item');
		grRitm.addEncodedQuery('sys_id=' + current.getUniqueValue() + '^' + gr.condition);
		grRitm.query();
		if (!grRitm.next()) {
			conditionFail = true;
		}
	}

	// 2. Evaluate scripted_condition if present
	if (gr.scripted_condition) {
		var evaluator = new GlideScopedEvaluator();
		evaluator.putVariable('source', current);
		evaluator.putVariable('answer', null);
		evaluator.evaluateScript(gr, 'scripted_condition', null);
		var result = evaluator.getVariable('answer');

		if (result !== true && result !== null) {
			scriptedFail = true;
		}
	}

	// 3. Final decision
	if (conditionFail || scriptedFail) {
		ans = 'no';
		workflow.scratchpad.current.shift(); // move to next item
	}

	return ans;
}

answer = checkCondition(current, workflow);
```


## 7. Current length zero

![image](https://github.com/user-attachments/assets/8df76359-677e-4d6a-93b9-38cef81f570f#.png)

```javascript
function checkCurrentArr(workflow){
	if(workflow.scratchpad.current.length == 0){
		return 'yes';
	}
	return 'no';
}
answer = checkCurrentArr(workflow);
```


## 8. Set the Now values

![image](https://github.com/user-attachments/assets/4240140b-2844-450b-88f3-b0be49eafac1#.png)

```javascript
(function(current, workflow) {
    workflow.scratchpad.now.catalog_item = '';
    workflow.scratchpad.now.short_desc = '';
    workflow.scratchpad.now.desc = '';
    workflow.scratchpad.now.event = '';
    workflow.scratchpad.now.fields = '';
    workflow.scratchpad.now.groups = '';
    workflow.scratchpad.now.title = '';
    workflow.scratchpad.now.type = '';
    workflow.scratchpad.now.variables = '';
    workflow.scratchpad.now.assign_user = '';
    workflow.scratchpad.now.assign_group = '';
    workflow.scratchpad.now.primary_worker = '';
    // workflow.scratchpad.now.business_service = '';
    workflow.scratchpad.now.service_offering = '';
    workflow.scratchpad.now.due_date = '';



    var grSrAtLines = new GlideRecord('x_hap_sr_flow_sr_at_lines');
    grSrAtLines.addEncodedQuery('sys_id=' + workflow.scratchpad.current[0].sysId);
    grSrAtLines.query();

    if (grSrAtLines.next()) {
        var grProperty = new GlideRecord('sys_properties');
        grProperty.get('name', 'x_hap_sr_flow.sr_flow.assign_order');
        var order = grProperty.value.toString().replaceAll(" ", "").split(",");

        workflow.scratchpad.now.catalog_item = grSrAtLines.catalog_item.getValue();
        workflow.scratchpad.now.event = grSrAtLines.event.getDisplayValue();
        workflow.scratchpad.now.fields = grSrAtLines.fields.getValue();
        workflow.scratchpad.now.groups = grSrAtLines.groups.getValue();
        workflow.scratchpad.now.title = grSrAtLines.title.getValue();
        workflow.scratchpad.now.type = grSrAtLines.type.getValue();
        workflow.scratchpad.now.variables = grSrAtLines.variables.getValue();
        workflow.scratchpad.now.primary_worker = grSrAtLines.primary_worker.getValue();

        // Setting the dynamic short description
        var short_desc = grSrAtLines.short_description;
        while (short_desc.indexOf("${") != -1) {
            var col = short_desc.split('${')[1].split("}")[0];
            var data = current.getDisplayValue(col) != null ? current.getDisplayValue(col) : '';
            short_desc = short_desc.substring(0, short_desc.indexOf("${")) + data + short_desc.substring(short_desc.indexOf("}") + 1);
        }
        workflow.scratchpad.now.short_desc = short_desc;

        // Setting the dynamic description
        var desc = grSrAtLines.description;
        while (desc.indexOf("${") != -1) {
            var col = desc.split('${')[1].split("}")[0];
            var data = current.getDisplayValue(col) != null ? current.getDisplayValue(col) : '';
            desc = desc.substring(0, desc.indexOf("${")) + data + desc.substring(desc.indexOf("}") + 1);
        }
        workflow.scratchpad.now.desc = desc;

        // Set assigned_to following the order at System Property
        var assign = '';
        if (grSrAtLines.getValue(order[0].toString())) {
            assign = chkValue(order[0].toString(), grSrAtLines.getValue(order[0].toString()));
        } else if (grSrAtLines.getValue(order[1].toString())) {
            assign = chkValue(order[1].toString(), grSrAtLines.getValue(order[1].toString()));
        } else if (grSrAtLines.getValue(order[2].toString())) {
            assign = chkValue(order[2].toString(), grSrAtLines.getValue(order[2].toString()));
        }

        var grUser = new GlideRecord("sys_user");
        grUser.addEncodedQuery("sys_id=" + assign);
        grUser.query();

        if (grUser.next()) {
            workflow.scratchpad.now.assign_user = grUser.getUniqueValue();
        } else {
            workflow.scratchpad.now.assign_group = assign;
        }

        // workflow.scratchpad.now.business_service = grSrAtLines.business_service;
        workflow.scratchpad.now.service_offering = grSrAtLines.service_offering;

        // Set the due_date. If the Reference Date is variable, find the variable value and calculate the date.
        if (grSrAtLines.reference_date != '' && grSrAtLines.reference_date.toString().includes(".")) {
            var grItem = new GlideRecord('item_option_new');
            grItem.addEncodedQuery('cat_item=' + current.cat_item + "^name=" + grSrAtLines.reference_date.split(".")[1]);
            grItem.query();

            if (grItem.next()) {
                var grMtom = new GlideRecord('sc_item_option_mtom');
                grMtom.addEncodedQuery('request_item=' + current.getUniqueValue() + '^sc_item_option.item_option_new=' + grItem.getUniqueValue());
                grMtom.query();

                if (grMtom.next()) {
                    var dtV = new GlideDateTime(grMtom.sc_item_option.value);
                    dtV.addDaysUTC(grSrAtLines.lead_time + 0);
                    workflow.scratchpad.now.due_date = dtV;
                }
            }
        } else {
            var refField = grSrAtLines.reference_date.toString();
            if (!gs.nil(refField)) {
                var refValue = current.getValue(refField);
                if (!gs.nil(refValue)) {
                    var dt = new GlideDateTime(refValue);
                    dt.addDaysUTC(grSrAtLines.lead_time + 0);
                    workflow.scratchpad.now.due_date = dt;
                } else {
                    workflow.warn("[WF Log] reference field '" + refField + "' has no value.");
                }
            } else {
                workflow.warn("[WF Log] reference_date field is empty.");
            }
        }

		// If the assign_user and assign_group is Empty. Assign the ServiceNow admin.
		if(workflow.scratchpad.now.assign_user == '' && workflow.scratchpad.now.assign_group == ''){
			var grAdmin = new GlideRecord('sys_user');
			grAdmin.addEncodedQuery('email='+gs.getProperty('x_hap_sr_flow.system_admin'));
			grAdmin.query();

			if(grAdmin.next()){
				workflow.scratchpad.now.assign_user = grAdmin.getUniqueValue();
			}
		}

        workflow.info("[WF Log] NOW value : " + JSON.stringify(workflow.scratchpad.now));
    }

    function chkValue(str, val) {
        var chk_val = "";
        val = val.toString();
        if (str == "groups") {
            chk_val = val;
        } else if (str == "variables") {
            var grVar = new GlideRecord("sc_item_option_mtom");
            grVar.addEncodedQuery(
                "request_item=" + current.getUniqueValue() + "^sc_item_option.item_option_new=" + val
            );
            grVar.query();
            if (grVar.next()) {
                chk_val = grVar.sc_item_option.value;
            }
        } else if (str == "fields") {
            var grRitm = new GlideRecord('sc_req_item');
            grRitm.addEncodedQuery("sys_id=" + current.getUniqueValue());
            grRitm.query();

            if (grRitm.next()) {
                chk_val = grRitm.getElement(val);
            }
        }
        return chk_val;
    }
})(current, workflow);
```


## 9. Check Line's Type - Approval

![image](https://github.com/user-attachments/assets/7a253568-2340-4a78-acab-7026756fec5a#.png)

```javascript
function typeApprove(current, workflow) {
	if(workflow.scratchpad.now.type == 'approve'){
		return 'yes';
	}
	return 'no';
}
answer = typeApprove(current, workflow);
```


## 10. Waiting Approval state = pending

![image](https://github.com/user-attachments/assets/ea99581e-afec-4ebe-9db2-9af7d9568bb8#.png)


## 11. Check the dynamic approval property

![image](https://github.com/user-attachments/assets/2b48c2ca-bd99-4873-bb29-9552099f38fa#.png)

```javascript
answer = checkProperty();
function checkProperty() {
    var gr = new GlideRecord('sys_properties');
    gr.addEncodedQuery('name=x_hap_sr_flow.dynamic_approval_on');
    gr.query();

    if (gr.next()) {
        var value = gr.value.toString();
        if (value == 'true') {
            return 'yes';
        }
    }

	return 'no';
}
```


## 12. Grant users Role

![image](https://github.com/user-attachments/assets/5f6e1466-a348-4390-9b9a-898cb9f38fc6#.png)

- Inputs
```javascript
var answer = {};
if (current.assignment_group) {
  answer['group'] = workflow.scratchpad.now.assign_group;
}
if (current.assigned_to) {
  answer['user'] = workflow.scratchpad.now.assign_user;
}
```


## 13. Check the "Auto Approve" system property

![image](https://github.com/user-attachments/assets/c4e1d90a-1a6e-4435-918e-b8479765b482#.png)

```javascript
answer = checkProperty();
function checkProperty() {
    var gr = new GlideRecord('sys_properties');
    gr.addEncodedQuery('name=x_hap_sr_flow.sr_flow.auto_approve');
    gr.query();

    if (gr.next()) {
        var value = gr.value.toString();
        if (value == 'true') {
            return 'yes';
        }
    }

	return 'no';
}
```


## 14. Check the "sr_flow.remove_duplicate_approve" system property

![image](https://github.com/user-attachments/assets/b40e0735-763f-4328-815c-66fb1b64952b#.png)

```javascript
answer = checkProperty();
function checkProperty() {
    var gr = new GlideRecord('sys_properties');
    gr.addEncodedQuery('name=x_hap_sr_flow.sr_flow.remove_duplicate_approve');
    gr.query();

    if (gr.next()) {
        var value = gr.value.toString();
        if (value == 'true') {
            return 'yes';
        }
    }

	return 'no';
}
```


## 15. Check approver has been approved the same ticket before

![image](https://github.com/user-attachments/assets/0459eb71-223c-4d47-8609-24b88f533e30#.png)

```javascript
answer = chkRequester(current, workflow);

function chkRequester(current, workflow) {
    var assignUserSysId = '';
    var groupsSysId = '';

    if (workflow && workflow.scratchpad && workflow.scratchpad.now) {
        if (!gs.nil(workflow.scratchpad.now.assign_user)) {
            assignUserSysId = String(workflow.scratchpad.now.assign_user);
        }
        if (!gs.nil(workflow.scratchpad.now.groups)) {
            groupsSysId = String(workflow.scratchpad.now.groups);
        }
    }

    // 보통은 'requested' 대상을 처리
    var gr = new GlideRecord('sysapproval_approver');
    gr.addQuery('document_id', current.getUniqueValue());
    gr.addQuery('state', 'approved'); // 또는 'not requested' 등 실제 상태에 맞추기
    gr.query();

    while (gr.next()) {
        var approverSysId = gr.getValue('approver');

		workflow.info('approver : ' + gr.approver.name);

        // 1) 지정 사용자와 동일
        if (assignUserSysId && approverSysId == assignUserSysId) {
            workflow.scratchpad.now.duplicate_approver = approverSysId;
            return 'yes';
        }

        // 2) 지정 그룹의 멤버인지
        if (groupsSysId) {
            var grMem = new GlideRecord('sys_user_grmember');
            grMem.addQuery('group', groupsSysId);
            grMem.addQuery('user', approverSysId);
            grMem.query();

            if (grMem.next()) {
                workflow.scratchpad.now.duplicate_approver = approverSysId;
                return 'yes';
            }
        }
    }
    return 'no';
}
```


## 16. Approval

![image](https://github.com/user-attachments/assets/c689d136-9b00-4fe0-8933-1ec28a264afe#.png)

![image](https://github.com/user-attachments/assets/9e6b82dd-f077-46b2-a30d-4261aaca8e9b#.png)

![image](https://github.com/user-attachments/assets/64e14256-6813-434b-8f8c-c776317fbfde#.png)

```javascript
answer = [];
answer.push(workflow.scratchpad.now.assign_user);
```

![image](https://github.com/user-attachments/assets/0f7bc13d-132b-4342-9001-654c9f718840#.png)

![image](https://github.com/user-attachments/assets/ab2932c5-ee1a-43ad-841a-93be24bbad58#.png)

```javascript
answer = workflow.scratchpad.now.groups;
```

## 17. Check requester is one of the approver

![image](https://github.com/user-attachments/assets/c94b12f3-adf8-4adb-be19-5d09b649c8e7#.png)

```javascript
answer = chkRequester(current, workflow);
function chkRequester(current, workflow){
	var requester = current.opened_by;

	if(workflow.scratchpad.now.assign_user != ''){
		if(requester == workflow.scratchpad.now.assign_user){
			workflow.scratchpad.now.duplicate_approver = requester;
			return 'yes';
		}
	}

	if(workflow.scratchpad.now.groups != ''){
		var grMem = new GlideRecord('sys_user_grmember');
		grMem.addEncodedQuery('group='+workflow.scratchpad.now.groups);
		grMem.query();

		while(grMem.next()){
			if(grMem.user == requester){
				workflow.scratchpad.now.duplicate_approver = requester;
				return 'yes';
			}
		}
	}
	return 'no';
}
```


</div>
<div class="lang-ko" markdown="1">
SR workflow 세부 내용


![image](https://github.com/user-attachments/assets/f8d778ca-e45f-407c-8c6e-f693a384e91a#.png)

## Properties

![image](https://github.com/user-attachments/assets/5197799b-77e2-43f4-a0ad-e80b613c2cc5#.png)

![image](https://github.com/user-attachments/assets/fa087e22-33d4-421b-89ea-ca8dbd93a519#.png)

![image](https://github.com/user-attachments/assets/8be28be2-1781-44e4-944d-cec8920e2b11#.png)

![image](https://github.com/user-attachments/assets/11f6817b-a3bd-40cf-9216-79e90a42cc7c#.png)

![image](https://github.com/user-attachments/assets/07ac4438-6852-407f-87bc-421546ce2b87#.png)

![image](https://github.com/user-attachments/assets/b8e5bc12-2a31-4808-b3cf-fd98beda50ec#.png)

![image](https://github.com/user-attachments/assets/eb2e6c80-1318-4420-aa1d-f4aefc9b74f9#.png)


## 1. Check the SR AT Lines Records

![image](https://github.com/user-attachments/assets/7e75ef71-f3b1-4aed-8e26-b4a43364403c#.png)


</div>
