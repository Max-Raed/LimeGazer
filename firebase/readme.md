
# 🔥 **Firebase Integration for LimeGazer**

To store gaze data from your LimeGazer survey, you can integrate Firebase for seamless data storage. This guide will show you how to upload gaze data in CSV format to Firebase storage.

---

## 🛠️ **Steps for Firebase Connection**

1. **Set Up Firebase**:
   - Create a Firebase project and set up storage.
   - Get the Firebase configuration keys and initialize Firebase in your LimeSurvey.

2. **Upload Gaze Data to Firebase**:
   Use the following code to store gaze data in CSV format and upload it to Firebase.

---

## 📜 **Firebase Storage Script**

```html
<script src="https://www.gstatic.com/firebasejs/7.7.0/firebase-app.js"></script><script src="https://www.gstatic.com/firebasejs/7.7.0/firebase-storage.js"></script><script src="https://player.vimeo.com/api/player.js"></script><iframe allowfullscreen="" frameborder="0" height="274" id="video" sandbox="" src="XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX width="640"></iframe><script>
   var prediction = webgazer.getCurrentPrediction();
   if (prediction) {
   var x = prediction.x;
   var y = prediction.y;
   }
         webgazer.setGazeListener(function(data, elapsedTime) {
             var webgazerVideoContainer = document.getElementById('webgazerVideoContainer');
             webgazerVideoContainer.style.display = 'none';
             if (data == null) {
                 return;
             }
             var xprediction = data.x; //these x coordinates are relative to the viewport
             var yprediction = data.y; //these y coordinates are relative to the viewport
           //console.log("xprediction:", xprediction);
           //console.log("yprediction:", yprediction);
           localStorage.setItem("GazePredictionX", xprediction);
           localStorage.setItem("GazePredictionY", yprediction);
         }).begin();
</script><script>
   var firebaseConfig = {
       apiKey: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX,
       authDomain: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       projectId: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       storageBucket: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       messagingSenderId: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       appId: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
   };
   
   var correctQid = "{VC01.qid}";
   var correctSgqa = "{VC01.sgqa}";
   var instrRead = "{INST01.sgqa}";
   var instrReadQid = "{INST01.qid}";
   var videoQid = "{VIDEO01.qid}";
   var correct;
   var videoContainer = "{VIDEO01.qid}"
   var video = document.getElementById('video');
   var supposedCurrentTime = 0;
   var isVideoFinished = false;
   var correctYes = document.getElementById('javatbd' + correctSgqa + 'Y');
   var correctNo = document.getElementById('javatbd' + correctSgqa + 'N');
   let iframe = document.querySelector('iframe');
   let player = new Vimeo.Player(iframe);
   let playing = false;
   let isFullscreen = false;
   let simulationTime = 0;
   let controls = false;
   let data = []; // Array to store the collected data
   
   function reloadFrame(){
   document.getElementById('video').src = document.getElementById('video').src;
   }
   
   function initialize() { 
   iframe.removeAttribute("sandbox");
   document.getElementById("ls-button-submit").style.display = "none";
     targList = document.getElementsByClassName("question-container");
   if (targList) {
   	for (var x = 1; x < targList.length; x++) {
     		targList[x].style.display = "none";
   }
   }
    // show video after instructions read
   document.getElementById('javatbd' + instrRead + 'Y').addEventListener('click', function(){
       document.getElementById("question" + videoContainer).style.display ="block";
    
   });
   
   document.getElementById('javatbd' + correctSgqa + 'Y').addEventListener('click', function(){
                document.getElementById("ls-button-submit").style.display = "inline-block";
           targList2 = document.getElementsByClassName("question-container");
         if (targList2) {
           for (var x = 2; x < targList2.length; x++) {
                 targList2[x].style.display = "inline-block";
             }
       }
   });
   
   document.getElementById('javatbd' + correctSgqa + 'N').addEventListener('click', function(){
    alert("Please reload the page");
   });
   
   // check for radio buttons
   document.getElementById("ls-button-submit").addEventListener('click', function(event){
     var counter = 0;
      radioList = document.getElementsByClassName("answertext");
         if (radioList) {
           for (var x = 0; x < radioList.length; x++) {
               inputList = radioList[x].childNodes;
                 inputList.forEach(input => {
                 if(input.value){
                   counter++;
                 }
               });
             }
       }
   //alert(counter + "  " + radioList.length);
   if(counter < radioList.length){
   event.preventDefault();
   alert("At least one question is missing. Please rate all questions");
   }
   });
   
   }
   document.addEventListener("DOMContentLoaded", function(event) {
   // DOM is ready, execute your code here
        initialize();
    reloadFrame();
   
   });
    firebase.initializeApp(firebaseConfig);
    console.log(firebase);
   
   
         player.on('play', function(e) {
             playing = true;
             //fullscreen();
             player.play();
             
            player.requestFullscreen().then(function() {
                 
            }).catch(function(error) {
              
            });
         });
   
   player.on('pause', function(e) {
             playing = false;
             // player.play();
             iframe.classList.remove(".disablehover")
             player.exitFullscreen().then(function() {
   
             }).catch(function(error) {
   
             });
             
         });
   
         player.on('ended', function(e){
               correct = document.getElementById("question" + correctQid);
           correct.style.display = "block";
           document.getElementById("question" + instrReadQid).style.display = "none";
           document.getElementById("question" + videoQid).style.display = "none";
      
           
             player.exitFullscreen().then(function() {
                      // Convert data to CSV format and save as a file
                          const csv = convertToCSV(data);
                       downloadCSV(csv, '{PROLIFICID.shown}_{GROUPNAME}.csv');
             }).catch(function(error) {
                 // an error occurred
             })
         });
   
   setInterval(function() {
   if (playing == true) {
   player.getCameraProps().then(function(cameraProps) {
   player.getCurrentTime().then(function(currentTime) {
     simulationTime = currentTime; // Update simulationTime with the resolved current timestamp
     var retrievedGazeX = localStorage.getItem("GazePredictionX");
     var retrievedGazeY = localStorage.getItem("GazePredictionY");
     var displayResX = window.screen.width;
   var displayResY = window.screen.height;
     data.push({ simulationTime, cameraProps, retrievedGazeX, retrievedGazeY, displayResX, displayResY}); // Store data in the array
     console.log(simulationTime, cameraProps, retrievedGazeX, retrievedGazeY, displayResX, displayResY);
   }).catch(function(error) {
     // Handle error if unable to get current time
   });
   }).catch(function(error) {
   // Handle error if unable to get camera properties
   });
   }
   }, 100);
     
   
   function convertToCSV(data) {
   const csvRows = [];
   const headers = ['video_timestamp', 'yaw', 'pitch', 'roll', 'gazeX', 'gazeY', 'displayResX', 'displayResY'];
   csvRows.push(headers.join(';'));
   
   for (const row of data) {
   const values = [
   row.simulationTime.toFixed(1),
   row.cameraProps.yaw,
   row.cameraProps.pitch,
   row.cameraProps.roll,
   row.retrievedGazeX,
   row.retrievedGazeY,
   row.displayResX,
   row.displayResY,
   ];
   csvRows.push(values.join(';'));
   }
   
   return csvRows.join('\n');
   }
   
   // Download CSV file
   function downloadCSV(csv, filename) {
   const csvBlob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
   const storageRef = firebase.storage().ref();
   const fileRef = storageRef.child("Daytime/Path/" + filename);
   
   fileRef.put(csvBlob)
   .then(function(snapshot) {
   console.log('CSV file uploaded to Firebase Storage successfully.');
   })
   .catch(function(error) {
   console.error('Error uploading CSV file to Firebase Storage:', error);
   });
   }
   
         // detect enter or exit fullscreen mode
         document.addEventListener('webkitfullscreenchange', fullscreenChange);
         document.addEventListener('mozfullscreenchange', fullscreenChange);
         document.addEventListener('fullscreenchange', fullscreenChange);
         document.addEventListener('MSFullscreenChange', fullscreenChange);
   
         function fullscreen() {
         // check if fullscreen mode is available
         if (document.fullscreenEnabled || 
             document.webkitFullscreenEnabled || 
             document.mozFullScreenEnabled ||
             document.msFullscreenEnabled) {
             
             // which element will be fullscreen
             var iframe = document.querySelector('iframe');
             // Do fullscreen
             if (iframe.requestFullscreen) {
             iframe.requestFullscreen();
             } else if (iframe.webkitRequestFullscreen) {
             iframe.webkitRequestFullscreen();
             } else if (iframe.mozRequestFullScreen) {
             iframe.mozRequestFullScreen();
             } else if (iframe.msRequestFullscreen) {
             iframe.msRequestFullscreen();
             }
         }
         else {
             document.querySelector('.error').innerHTML = 'Your browser is not supported';
         }
         }
   
         function fullscreenChange() {
             isFullscreen = !isFullscreen;
             if(isFullscreen == false){
                 player.pause()
             }
         }
   
         
</script>
```

---

## 🔍 **How Firebase Integration Works**

- **Data Storage**: This script stores gaze data (in CSV format) as a `Blob` and uploads it to a specified Firebase path.
- **Firebase Setup**: Make sure you have properly set up Firebase authentication and storage rules to allow file uploads.

For more detailed documentation on Firebase setup, visit the [Firebase documentation](https://firebase.google.com/docs).
