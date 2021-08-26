# rtcproctor

## RTCProctor: real-time peer-to-peer video proctoring

This is a program written for video proctoring of examinations conducted off-school/off-campus, or exam from home. The main idea is to use each student's mobile phone as a video camera, and a web-based peer-to-peer streaming code to send live picture to a proctor computer. A proctor can monitor multiple videos streamed from students in one page. More than one proctor can monitor any of the students' video streams at the same time. Video delivery is based on the WebRTC real-time communication standard. The code was written based on RTCMulticonnection library (see: https://github.com/muaz-khan/RTCMultiConnection and ref. at the end of this document).

Although on the student side the software can be used with a notebook computer, a mobile phone is a prefered device because its front-facing camera has a wider and more flexible viewpoint.

### Features

*  No video server is required (however, a signaling server as well as ICE server are still needed to establish a connection. See installation guide below on how to get them.)  
*  Video (picture & sound) is streamed one-way from each student to his/her proctor(s). Students cannot see videos from others.
*  Installation-free on a client side. Only a web browser supporting WebRTC is needed on both students and proctors machines.
*  Proctor can choose to chat (send/receive text) with each student.
*  Student/proctor can send attention request messsage (and play an alert sound) to the other end of the connection.
*  List of off-air users (those with no video stream detected).
*  Camera selection for a device with more than one video cameras.
*  Auto offline detection and reconnection for Student Page.
*  Can be used on its own, or along with its Moodle LMS plugin counterpart.

###  How to Use
1. Stream codes are exchanged between a proctor and his/her students before the exam. How the codes can be exchanged are explained in the next section.
2. Each student use a browser to open a 'RTCProctor - Student Page', and fill in his/her stream code. Then, press 'Go Live'. This should create a video stream available to anyone who know the code.
3. A proctor open a 'RTCProctor - Monitor Page'. Add a list of student-streamcode pairs to a textbox shown on the first page. Press 'Next' to start receiving video streams from students.
4. Any time during the live connection, a student or proctor can request attention by clcking on the 'hand-waving' icon on the top-right corner of the chat message box. Both side and exchange text message with each other.
5. For a proctor to view individual video, as well as start a chat with each student, click on a student name/id below the video box.

Note that, because a student acts like a broadcaster and a proctor acts like a receiver, there can be more than one proctors viewing student streams at the same time. Also, whatever happens to a proctor machine does not interrupt video streaming. Also, importantly, due to a variety of mobile phone devices in use these days, some of students' device may not yet support WebRTC. If so, RTCProctor may not function properly on these devices.

A live demo is available at https://rtcproctor.herokuapp.com  (However, this site runs on a Heroku free plan, with limited quota on usage transaction. Therefore, availability can be limited and not guaranteed). 
*  A student page: https://rtcproctor.herokuapp.com/rtcproctor-s.html
*  A proctor (monitor) page: https://rtcproctor.herokuapp.com/rtcproctor-m.html

###  How to Exchange Stream Codes
One of the most important tasks to do in arranging this online proctoring method is to create and exchange stream codes between students and their proctor(s). The stream codes are supposed to be a secret piece of information shared only by each student and his/her proctor. Two methods are suggested for the task.
1. Use a companion Moodle plugin. If you have already used Moodle LMS at your shool, this is a more convenient choice.
2. Use Google sheet and email to exchange stream codes. This is for those who don't have or use Moodle.

For Choice #1, here is a link to early, experimental version of a companion Moodle plugin to be used with RTCProctor (https://github.com/nopparuts/rtcproctor-plugin.git). For Choice #2, follow instruction below.

#### Exchange stream codes by Google sheet + Gmail
1. Create a Google sheet to generate to codes and prepare a message to send to each student. An example of a spreadsheet, which auto generates a randome stream code, and prepare a link + code for each student, is given here https://github.com/nopcho/rtcproctor/blob/main/rtcproctor-codeexchange.xlsx (in excel format. You need to upload this to Google Drive, and convert to Google Sheet before proceeding to the next step).
2. Write code to send email from Google sheet. Please follow this link for instruction: https://developers.google.com/apps-script/articles/sending_emails?hl=th&authuser=0
3. Run the script from Google sheet. The link or code (depending on how you prepare the message), will be sent to each student mail box. From there, a student can use information provided to stream his/her video with 'RTCProctor Student Page'.
4. A proctor can copy the list of student-code pairs from the sheet, and paste into the first page of 'RTCProctor - Monitor Page', and start monitoring student videos.

###  Installation Guide
Three pieces of software/sytem are required for the code to work:
1.  RTCProctor software itself, as shared by this repo.
2.  A socketio signaling server, used to initialze a connection.
3.  An ICE server, used along with a signaling server to establish a peer-to-peer connection

Depending on a signaling server configuration, RTCProctor code may be installed and run on student/proctor machines, without the need for a web server. Otherwise, it can be hosted on any static web server.

A signaling server is used to publish and initialize video stream connection. A nodejs version of the server software are available from https://github.com/muaz-khan/RTCMultiConnection-Server. List of some public signaling servers for testing include:
* https://rtcmulticonnection.herokuapp.com:443/  (note: may no longer available for public use)
* https://webrtcweb.com:9002/ (note: may no longer available for public use)
* https://rtcsignalserver.herokuapp.com/ (note: this server runs under the Heroku free plan. Therefore, performance and availability is limited. When usage exceeds the quota, the server will not be available.)

For production use, it is recommended that you should run your own signaling server, to get reliable service as well as good security.

For the third part, ICE server is needed in real-world application of WebRTC peer-to-peer video streaming. There are two types of ICE servers, STUN and TURN ICE servers. Free STUN servers are available, and you can use the default set by RTCProctor. However, sometimes only STUN server is not enough to establish a connection. This happens when student devices are connected through some service providers with strict Firewall and NAT rules. When this is the case, a TURN server is needed. Unfortunately, there is no known public TURN server because the server consumes a lot more computing and networking resource, compared to what required to run STUN servers. To ensure reliable use of RTCProctor, users need to find or run their own TURN server.

###  Install Your Own Signaling Server on Heroku Server

Here is a guide on how to install and run the signaling server on Heroku cloud service. A basic knowledge of Linux commands is needed.

1. Create an account on Heroku site (www.heroku.com). You may start by choosing a free plan. 
2. Follow instruction from Heroku website to create a NodeJS server, and prepare your local Heroku development environment.
3. Go to https://github.com/muaz-khan/RTCMultiConnection-Server and download the RTCMulticonneciton socketio signaling server software. 
4. Install the signaling software onto your local Heroku machine. At this point, you may need to make some change to the original code in server.js:

   comment or delete the following line 
    
    `var PORT = 9001; //comment this line`  

   and add the one shown below instead.

    `var PORT = process.env.PORT; //add this line instead`
    

   In addition, you might need to set CORS configuration to allow RTCProctor code to access the server from different domain. For example, to allow your signaling server to be accessible from RTCProctor pages located on any server (or on a local machine), make change to `server.js` as suggested below.
   
   ####   From (only accessible from pages hosted on the same domain)
   
   ```// --------------------------
   // socket.io codes goes below
   
   ioServer(httpApp).on('connection', function(socket) {
      RTCMultiConnectionServer.addSocket(socket, config);  
   ```
   
   ####   To (required to access from anywhere)
   
   ```// --------------------------
   // socket.io codes goes below
   
   var iooptions = {
      cors: {
        origin: "*",
        methods: ["GET", "POST"],
        credentials: true
      }
   };
   
   ioServer(httpApp, iooptions).on('connection', function(socket) {
      RTCMultiConnectionServer.addSocket(socket, config);
   ```

5. Add a text file named `Procfile` to your server root directory containing this single line

    `node server.js`  

7. Use git to push the whole directory to your Heroku server, and the signaling server should start and run automatically.
8. Set the SocketIO URL in `rtcproctor-*.html` files  to your signaling server (something like https://yoursappname.herokuapp.com/)

###  Install Your Own ICE Server

coTURN (https://github.com/coturn/coturn) is a popular free STUN and TURN server. Links to installation guide are listed below.

*  https://gabrieltanner.org/blog/turn-server
*  http://truelogic.org/wordpress/2020/06/17/setup-your-own-stun-turn-server-using-coturn/


