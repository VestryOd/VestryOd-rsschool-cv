## **MAKSYM KASPRIV**

+38 097 669 73 83 · Viber +38 095 323 52 52 · Odessa, Ukraine · vestry.odessa@gmail.com

### SUMMARY
Reliable, energetic and resourceful web development specialist. I’m constantly learning, and plan React courses in February 2020. Interesting in project management. I have experience in website design and scripting. In addition, an expert in the field of communication and building relationships with clients, extensive experience in teamwork.

### SKILLS
* Languages: PHP (basics), Javascript, HTML, CSS [example](https://vestryod.github.io/acrostia/);
* Web libs: jQuery, Materialize.css, Bootstrap, Animate.css;
* Web Dev tools: Gulp, npm;
* QA: Exploratory, Ad Hoc & Functional testing, checklists and test cases, bug reporting;
* VCS: Git basics;
* DB: MySQL (basic set of queries);
* Graphic editors: CorelDRAW, ADOBE Photoshop.

### Example 

This code was developed for the **Google Apps Script** platform, which understands only the **ES05** syntax.

```javascript
/* get string from 'action' select

  return {}. Keys: 
    id:         number or 'new'
    info:       object with data from inputs
    stage:      only 'details' changes
    task:       if need finish task - true/false
    reminder:   {} keys:
       id:      'id' of existence reminder or empty
       info:    all info from reminders inputs
       
*/
function prepareAndSendRecord(selectedAction) {
    var resultObject = {};

    var idElem = document.querySelector('#form #id');
    var idValue = idElem.value;
    var error = false;
    var addReminderElem = document.querySelector('#add-reminder input');
    var reminderId = document.querySelector('#reminder-id');

    if (selectedAction == 'Выбрать действие') {
        reportModalShow({ type: 'error', title: 'Ошибка! Что-то пошло не так...', text: 'Выберите необходимое действие для введенной записи!' });
        error = true;
    } else if (selectedAction == 'Внести/сохранить изменения') {
        resultObject.stage = undefined;
        if (idValue != 'new') {
            var recordsData = saveRecords();
            var recordsState = getProjectRecordStates();

            resultObject.info = checkEqualObjects(recordsState, recordsData);
        } else {
            resultObject.info = saveNewRecords();
        }
        if (addReminderElem.checked == true) {
            var rem = {};
            var reminderData = saveReminder();

            if (reminderId.dataset.id != '') {

                rem.id = reminderId.dataset.id + '@google.com';

                var reminderState = getReminderStates();
                var difference = checkEqualObjects(reminderState, reminderData);

                if (difference['reminder-date'] || difference['reminder-time'] || difference['reminder-duration']) {
                    rem.info = prepareReminderObjToSpreadsheet(reminderData);
                } else {
                    rem.info = difference;
                }
            } else {
                rem.id = undefined;
                rem.info = prepareReminderObjToSpreadsheet(reminderData);
            }
            resultObject.reminder = rem;
        } else {
            resultObject.reminder = undefined;
        }
    } else if (selectedAction == 'Сменить этап') {
        if (idValue == 'new') {
            reportModalShow({ type: 'error', title: 'Ошибка! Что-то пошло не так...', text: 'Смена этапа возможна только у существующего проекта!' });
            error = true;
        } else {
            resultObject.info = undefined;
            resultObject.reminder = undefined;
            var shangeData = changeStage();// {stage, done}

            resultObject.stage = shangeData;
        }
    } else if (selectedAction == 'Завершить задачу') {
        if (idValue == 'new') {
            reportModalShow({ type: 'error', title: 'Ошибка! Что-то пошло не так...', text: 'Завершить можно только существующую задачу!' });
            error = true;
        } else {
            resultObject.stage = undefined;
            resultObject.task = true;
            resultObject.info = finishTask();
            if (reminderId.dataset.id != '') {
              resultObject.reminder = {};
              resultObject.reminder.id = reminderId.dataset.id + '@google.com';
            } else {
              resultObject.reminder = undefined;
            }   
        }
    } else if (selectedAction == 'Изменить/сохранить напоминание') {
        resultObject.info = undefined;
        resultObject.stage = undefined;

        if (addReminderElem.checked == true) {
            var rem = {};
            if (reminderId.dataset.id != '') {
                rem.id = reminderId.dataset.id + '@google.com';

                var reminderData = saveReminder();
                var reminderState = getReminderStates();

                var difference = checkEqualObjects(reminderState, reminderData);
                if (difference['reminder-date'] || difference['reminder-time'] || difference['reminder-duration']) {
                    rem.info = prepareReminderObjToSpreadsheet(reminderData);
                } else {
                    rem.info = difference;
                }
            } else {
                var reminderData = saveReminder();
                rem.id = undefined;
                rem.info = prepareReminderObjToSpreadsheet(reminderData);
            }
            resultObject.reminder = rem;
        }
    } else if (selectedAction == 'Удалить напоминание') {
        if (reminderId.dataset.id == '') {
            reportModalShow({ type: 'error', title: 'Ошибка! Что-то пошло не так...', text: 'Удалить можно только существующее напоминание!' });
            error = true;
        } else {
            resultObject.info = undefined;
            resultObject.stage = undefined;

            var rem = {};
            rem.id = reminderId.dataset.id + '@google.com';
            rem.info = undefined;
            rem.clear = true;
            resultObject.reminder = rem;
        }
    }

    if (resultObject.info == undefined && resultObject.reminder == undefined && resultObject.stage == undefined && resultObject.task == undefined) {
        reportModalShow({ type: 'error', title: 'Ошибка!', text: 'Запись пуста. Добавьте данные для сохранения!' });
    } else {
        if (!error) {
            resultObject.id = idValue;
        }
        return resultObject;
    }
}
```