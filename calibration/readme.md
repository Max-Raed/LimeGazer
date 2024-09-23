
# 🎯 **Calibration Setup for LimeGazer**

In order to ensure accurate eye-tracking results using LimeGazer, you need to set up the calibration process properly. This guide will walk you through the necessary steps.

---

## 🚀 **Steps for Calibration Setup**

1. **Create a Calibration Page**: 
   Add a new question group or a page dedicated to calibration in your LimeSurvey.

2. **Insert the Calibration Code**:
   Use the following script to start the Webgazer calibration process and collect user gaze data.

---

## 📜 **Calibration Script**

```html
To calibrate the eye tracker click on ALL red squares. IMPORTANT: Move the mouse to the square, LOOK AT THE SQUARE SO THAT THE RED DOT IS NEAR THE SQUARE. click on the square once until it turns green. <script>
   var prediction = webgazer.getCurrentPrediction();
   if (prediction) {
   var x = prediction.x;
   var y = prediction.y;
   }
   console.log(prediction.x);
         webgazer.setGazeListener(function(data, elapsedTime) {
             var webgazerVideoContainer = document.getElementById('webgazerVideoContainer');
             webgazerVideoContainer.style.display = 'none';
             if (data == null) {
                 return;
             }
             var xprediction = data.x; //these x coordinates are relative to the viewport
             var yprediction = data.y; //these y coordinates are relative to the viewport
             //console.log("xprediction", xprediction, "yprediction", yprediction); //elapsed time is based on time since begin was called
         }).begin();
   webgazer.clearData();
</script>
<style type="text/css">html, body {
   height: 100%;
   margin: 0;
   padding: 0;
   overflow: hidden; /* Prevent vertical scrollbar */
   }
   article {
   position: relative;
   width: 100%; /* Set width to 100% */
   height: 100%; /* Take up full viewport height */
   border: 1px solid black;
   }
   .custom-checkbox {
   position: absolute;
   width: 20px;
   height: 20px;
   background-color: red; /* Red color for unchecked checkboxes */
   appearance: none;
   -webkit-appearance: none;
   border: 1px solid black;
   }
   .custom-checkbox:checked {
   background-color: green; /* Green color for checked checkboxes */
   }
</style>
<script>
   // Function to create a custom checkbox
   function createCustomCheckbox(x, y) {
       const checkbox = document.createElement('input');
       checkbox.type = 'checkbox';
       checkbox.className = 'custom-checkbox';
       checkbox.style.left = x + 'px';
       checkbox.style.top = y + 'px';
       document.querySelector('article').appendChild(checkbox);
   }
   
   // Get article dimensions
   const article = document.querySelector('article');
   
   // Create custom checkboxes in each corner (top-left, top-right, bottom-left, bottom-right)
   createCustomCheckbox(0, 0);
   createCustomCheckbox(article.clientWidth - 20, 0);
   createCustomCheckbox(0, article.clientHeight - 20);
   createCustomCheckbox(article.clientWidth - 20, article.clientHeight - 20);
   
   // Create custom checkboxes in the middle of each edge (top, bottom, left, right)
   createCustomCheckbox(article.clientWidth / 2 - 10, 0);
   createCustomCheckbox(article.clientWidth / 2 - 10, article.clientHeight - 20);
   createCustomCheckbox(0, article.clientHeight / 2 - 10);
   createCustomCheckbox(article.clientWidth - 20, article.clientHeight / 2 - 10);
   
   // Create custom checkbox at the center
   createCustomCheckbox(article.clientWidth / 2 - 10, article.clientHeight / 2 - 10);
   
   
</script> <script>
   setInterval(function() {
           const checkboxes = document.querySelectorAll('.custom-checkbox');
   
      		let allChecked = true;
           checkboxes.forEach(checkbox => {
               if (!checkbox.checked) {
                   allChecked = false;
               }
           });
           console.log(allChecked);
           const donebutton = document.getElementById('answer265399X8157X110721SQ001');
   
           if (allChecked) {
             donebutton.checked = true;
           } else {
               donebutton.checked = false;
           }
       }, 100);
</script>
```

This code will capture gaze prediction and store it in the `localStorage` for access by other scripts.


The calibration data is then stored locally in the user's browser across sessions by [localforage](https://localforage.github.io/localForage/).

Note that the 'answer265399X8157X110721SQ001' must be modified to match the ID of the checkbox and that the question must be a mandatory question with a checkbox as the answer.

![](Images/calibration.png)

💡 For debugging, it can be helpful to delete this data manually on a regular basis. You can find it in the screenshot. Delete the webgazerGlobalData and webgazerGlobalSettings entry.

---

## 🔍 **How Calibration Works**

- **Webgazer**: Tracks the user's gaze position and predicts where on the screen they are looking.
- **Data Storage**: Predicted X and Y coordinates are saved locally, which can then be used across multiple questions or screens within the same survey.

Make sure to run this script at the beginning of your survey to calibrate the eye-tracker for the user.
