# A Web Chat Systems with Authentication Microservices

1. Introduction

On the whole, the project consists of 2 microservices, where one microservice consists of the Single-page Web Chat server (HTML & JavaScript) and the other microservice consists of the socket.io web server (For Data validation and insertion) which is linked to the database.
Below, I have attached the links all the repositories used and microservices deployed.

Repository – tumukuntaa1
https://bitbucket.org/cca-tumukuntaa1/tumukuntaa1/src/master/
This repository (private) consists of the front-end UI code.

Repository – account-microservice 
https://bitbucket.org/cca-tumukuntaa1/account-microservice/src/master/
This repository consists of the code for user authentication and insertion.

Login and Signup Deployed on Heroku (Microservice1)
https://cca-tumukuntaa1-microservice1.herokuapp.com/
This doesn’t have a UI, this is just to implement the user validation and insertion.

Front-End UI Deployed on Azure (Microservice2)
https://cca-tumukuntaa1-microservice2.azurewebsites.net/
This consists of the front-end UI deployed on Azure which uses the microservice1 deployed on Heroku.

 
2. Design and Implementation

My front-end UI has a simple design and focusses more on feature.
Its implementation is divided into two parts,
a. User Authentication 
Algorithm:
•	Here, in my UI when user enters a username and password, then JavaScript sends that submitted credentials to socket.io web server.
•	These details are searched in the “users” collection of my cca-labs database for user authentication.
•	When the user is present in the database, then it returns the user to socket.io and “Welcome!” message is returned to the front-end by the socket.io server. If not it returns an error to the socket.io and it returns “Sorry, user does not found” to the front-end. 
•	The UI checks for the “Welcome” keyword in the returned message, if it is present, then it displays an alert “Welcome!” and changes the UI to Chat UI. If not, it displays an alert stating “Sorry, user does not exist”.

b. User Insertion 
Algorithm:
•	Here, the user enters username, password, email and fullname. All these details are sent to socket.io server and the username is passed to ‘users’ collection.
•	 It returns the user to socket.io, when username matches. It returns ‘user already exists’ to front-end and, this is displayed as an alert. 
•	If the user doesn’t exist, then socket.io returns “User inserted successfully”. Else if there’s any error it returns “cannot insert into database”.
•	These are displayed as alerts after insertion and the UI is redirected to /login.














Data Flow in the whole assignment across different microservices is shown in the form of a sequence diagram below,

 


 
3. Demo (Screenshots and Video)
i. Screenshots
A user with valid credentials is allowed to login,
 
User after logging in,
 
Registering a user,
 
After successful registration,
 

After successful registration, it redirects to /login page.
 
Simulating the web chat application, one of the user is typing,
 




Authenticated users sending messages to each other,
 
When a user who doesn’t exist tries to login,
 

A couple of screenshot of “users” collection,

 

 






When a user who already exist tries to login,
 
A screenshot of console while a user gets connected and sends a message is shown below,
 


Checking data flow in the microservice /login through Postman,
 
With invalid input,
 
Signing up user through Postman (To check data flow),
 

When user already exists,
 
While implementation, I couldn’t take few screenshots of the data flow.

ii. Video

Link for the demo video is given below,
https://drive.google.com/file/d/1kdREAMmZHzPoqI9V79RYTJoqFgRNmN_4/view



 
4. Appendix – Source Code

Index.html (repo – tumukuntaa1)
<!DOCTYPE html>
<html>
    <head lang="en">
        <meta charset="utf-8">
        <title>CCA-Assignment of Abhinava K. Tumukunta</title>
        <link rel="stylesheet" href="https://udayton-cloud.bitbucket.io/style3.css">
        <script src="https://udayton-cloud.bitbucket.io/clock.js"></script>
        <script src="https://code.jquery.com/jquery-3.5.1.min.js" crossorigin="anonymous"></script>
        <style>
            .button{
                background-color: #4CAF50;
                border: none;
                color: white;
                padding: 5px;
                text-align: center;
                text-decoration: none;
                display: inline-block;
                font-size: 12px;
                margin: 4px 2px;
                cursor: pointer;
            }
            .round{
                border-radius: 8px;
            }
            #response{
                background-color: #FF9800;
            }
        </style>
        <script src="/socket.io/socket.io.js"></script> 
        <script>
            var socketio=io();
            socketio.on("login",(data)=>{
                if(data.includes("Welcome")){
                    $('#chat-page').show();
                    $('#login').hide();
                }else{
                    alert(data);
                }
            });

            socketio.on("signup",(data)=>{
                console.log(data)
                if(data.includes("success")){
                    $('#login').show();
                    $('#signup').hide();
                    alert("signup done!, you can login now.");
                }else{
                    alert(data);
                }
            })
        </script>  
    </head>
    <body>
        <div class="container wrapper">
        <div class="top">
        <h1>Cloud Computing and Applications</h1>
        <h2>Individual Assignment By Abhinava K Tumukunta</h2>
        <div id="email" onclick="showhideEmail()">Show my email</div></div>
        <script src='email1.js'></script>
        <div class="wrapper">
        <div class="menubar">
        <p>Just a demo HTML page.</p>
        <div id="mydiv"
                onclick="document.getElementById('mydiv').innerHTML= 'Clicked time: '+Date()">
            Click here for Current time
        </div>
        <div id="digital-clock"></div>
        <script>
            function displayTime(){
                document.getElementById('digital-clock').innerHTML='Current time:' + new Date();
            }
            setInterval(displayTime,500);
        </script>
        </div>
        <div class="main">
        <canvas id="analog-clock" width="150" height="150" style="background-color: #999"></canvas>
        <script>
            var canvas = document.getElementById("analog-clock");
            var ctx = canvas.getContext("2d");
            var radius=canvas.height /2;
            ctx.translate(radius, radius);
            radius = radius * 0.9;
            setInterval(drawClock, 1000);

            function drawClock(){
                drawFace(ctx, radius);
                drawNumbers(ctx, radius);
                drawTime(ctx, radius);
            }
        </script>
        <div id="login">
            <form>
                <h3>LOGIN</h3>
                <input name="username" placeholder="USERNAME" id="username-login"><br>
                <input name="password" placeholder="PASSWORD"  id="password-login" type="password"><br>
                <div class="button" onclick="login()">LOGIN</div>
                <p>Don't have an account? <a href="#" onclick="$('#signup').show(); $('#chat-page').hide(); $('#login').hide();">Signup!</a></p>
            </form>
            <script>
                function login(){

                    socketio.emit("login",{"username":$('#username-login').val(),"password":$('#password-login').val()});
                    
                }
            </script>
        </div>

        <div id="signup">
            <form>
                <h3>SIGNUP</h3>
                <input name="username" placeholder="USERNAME" id="username"><br>
                <input name="password" placeholder="PASSWORD"  id="password" type="password"><br>
                <input name="email" placeholder="EMAIL" id="email" type="email"><br>
                <input name="fullname" placeholder="FULL NAME"  id="fullname"><br>
                <div class="button" onclick="signup()">SIGNUP</div>
            
            </form>
            <script>
                function signup(){

                    alert($('#username').val())
                    socketio.emit("signup",{"username" : $('#username').val() , "password" : $('#password').val(),
            "email" : $('#email').val(), "fullname" : $('#fullname').val()});
                   
                }
            </script>
        </div>

        <div id="chat-page">
            <form>
            <h3>Chat Client</h3><br>
            Chat message:<input name="data" onkeyup="socketio.emit('typing')" onkeypress="checkEnter(event)" id="data">
            <input class="button round" type="button" value="Send" onclick="send()">
            <div id="typing"></div>
            <div id="online"></div>
            <div id="response"></div>
            <script>
                function send(){
                    var input=$('#data').val();
                    if (input.length == 0){return;}
                    socketio.emit("message",input)
                    $("#data").val("")
                }
                socketio.on("message",(data)=>{
                    $("#response").append(data+"<br>");
                })
                socketio.on("typing",function(data){
                    $("#typing").html(data);
                    setTimeout(()=>{$("#typing").html("<br>")},500);
                });
                socketio.on("online",(data)=>{
                    $("#online").html(data+"<br>");
                });
                function checkEnter(event){
                    if(event.keyCode==13) send();
                }
            </script>
            <!--<script>
                function jQueryAjax(){
                    var input=$("#data").val();
                    if (input.length == 0){return;}
                    $.get("echo.php?data="+input,
                        function(result){
                            $("#response").html("Response from server:"+result);
                        });
                        $("#data").val("");
                }
                function jQueryAjaxPost(){
                    var input=$("#data").val();
                    if (input.length == 0){return;}
                    $.post("echo.php",
                    {data: input},
                    function(result){
                        $("#response").html("Response from server:"+result);
                    });
                    $("#data").val("");
                }
            </script>-->
        </form>
        <script>
            $('#chat-page').hide();
            $('#signup').hide();
        </script>
    </div>
        <!--
        <script>
            function getEcho(){
                var input = document.getElementById("data").value;
                if (input.length ==0){
                    return;
                }
                var xhttp= new XMLHttpRequest();
                xhttp.onreadystatechange = function() {
                    if( this.readyState == 4 && this.status== 200){
                        console.log("Received data="+ xhttp.responseText);
                        document.getElementById("response").innerText= "Response from server:"+ xhttp.responseText;
                    }
                }
                xhttp.open("GET","echo.php?data="+input,true);
                xhttp.send();
                document.getElementById("data").value="";
            }
        </script>-->
        </div>
        </div>
        </div>
    </body>
</html>

Index.js (repo – tumukuntaa1)
const express = require('express')
const needle = require('needle');
const app = express()
const MongoClient = require('mongodb').MongoClient;
const mongourl = "mongodb+srv://cca-tumukuntaa1:saireddy16@cca-tumukuntaa1.lvn4k.mongodb.net/cca-labs?retryWrites=true&w=majority";
const dbClient = new MongoClient(mongourl,{useNewUrlParser: true, useUnifiedTopology: true});
dbClient.connect(err=>{ 
    if(err) 
        throw err;
    console.log("Connected to the MongoDB cluster");
})
const cors = require('cors')
app.use(cors())
var port = process.env.PORT || 8080;
const http = require('http').Server(app).listen(port)
app.use(express.static('static'))
app.use(express.urlencoded({extended: false}))

app.get('/',(req,res)=>{
    res.sendFile(__dirname+'/static/chatclient/index.html');
})

app.get('/echo.php',function(req,res){
    var data = req.query.data
    res.send(data)
})

app.post('/echo.php',function(req,res){
    var data = req.body['data']
    res.send(data)
})
app.get('/uscities-search',(req,res)=>{
    res.send("US City search in Microservice by Abhinava K. Tumukunta");
})
let fields = {_id: false,
    zips : true,
    city : true,
    state_id: true,
    state_name: true,
    county_name: true,
    timezone: true};
    app.get('/uscities-search/:zips(\\d{1,5})', function(req,res){
        const db = dbClient.db();
        let zipRegEx = new RegExp(req.params.zips);
        const cursor = db.collection("uscities").find({zips:zipRegEx}).project(fields);
        cursor.toArray(function(err, results){
            console.log(results);
            res.send(results);
        });
    });
    app.get('/uscities-search/:city', function(req,res){
        const db = dbClient.db();
        let cityRegEx = new RegExp(req.params.city, 'i');
        const cursor = db.collection("uscities").find({city:cityRegEx}).project(fields);
        cursor.toArray(function(err, results){
            console.log(results);
            res.send(results);
        });
    });
const io = require('socket.io')(http);
console.log("socket.io server is running on port "+port);
app.get("/chat",(req,res)=>{
    res.sendFile(__dirname+'/static/chatclient/index.html')
})
io.on('connection',(socketclient)=>{
    console.log('A new client is connected!');
    socketclienthandler(socketclient)
})

function socketclienthandler(socketclient){
    socketclient.on("message", (data)=>{
        console.log('Data from a client: '+data);
        io.emit("message",`${socketclient.id} says: ${data}`)
    });
    socketclient.on("typing", ()=>{
        console.log(`${socketclient.id} is typing ...  `);
        socketclient.broadcast.emit("typing",`${socketclient.id} is typing ...`)
    });
    var onlineClients = Object.keys(io.sockets.sockets).length;
    var welcomeMessage = `${socketclient.id} is connected! Number of connected clients: ${onlineClients}`;
    console.log(welcomeMessage);
    io.emit("online",welcomeMessage); 
    socketclient.on('disconnect', ()=>{
        var onlineClients = Object.keys(io.sockets.sockets).length;
        var byeMessage = `${socketclient.id} is disconnected! Number of connected clients: ${onlineClients}`;
        console.log(byeMessage);
        io.emit("online",byeMessage); 
    });

    socketclient.on('login', (data)=>{
        console.log(data)
        needle('post', 'https://cca-tumukuntaa1-microservice1.herokuapp.com/login', data, {json: true}).then((res) => {
            console.log(`Status: ${res.statusCode}`);
            console.log('Body: ', res.body);
            socketclient.emit("login", res.body)
        }).catch((err) => {
            console.error(err);
        });
    });

    socketclient.on('signup', (data)=>{
        console.log(data)
        needle('post', 'https://cca-tumukuntaa1-microservice1.herokuapp.com/signup', data, {json: true}).then((res) => {
            console.log(`Status: ${res.statusCode}`);
            console.log('Body: ', Object.keys(res));
            socketclient.emit("signup", res.body)
        }).catch((err) => {
            console.error(err);
        });
    });
}
 
Index.js (repo – account-microservice)
const express=require('express')
const app=express()
var port= process.env.PORT || 8080;
const cors=require('cors')
app.use(cors())
app.use(express.json());
app.use(express.static('static'))
app.listen(port,() =>
console.log('Http Server with Express.js is listening on port: '+port))
const MongoClient = require('mongodb').MongoClient
const mongourl = "mongodb+srv://cca-tumukuntaa1:saireddy16@cca-tumukuntaa1.lvn4k.mongodb.net/cca-labs?retryWrites=true&w=majority"
const dbClient = new MongoClient(mongourl, {useNewUrlParser: true, useUnifiedTopology:true});
dbClient.connect(err=> {
    if (err) throw err;
    console.log("Connected to MongoDB Cluster!");
});
app.post('/',(req,res)=>{
    res.sendFile('https://cca-tumukuntaa1-microservice1.herokuapp.com/');
})
app.post('/signup',(req,res)=>{
    const db = dbClient.db();
    console.log(req.body)
    const {username,password,fullname,email} = req.body; //input
    console.log('/singup->req.body.username:'+username);
    console.log('/singup->req.body.username:'+username);    
    db.collection("users").findOne({username:username},(err,user)=>{
            if(user){
                res.send("User already exists!")
            }
            else{
                let newUser = {username:username,password:password,fullname:fullname,email:email};
                db.collection("users").insertOne(newUser,(err,result)=>{
                    if(err){
                        res.send("Cannot insert into database.")
                    }
                    res.send("User inserted successfully.")
                });
            }
        });
})

app.post('/login',(req,res)=>{
    const db = dbClient.db();
    const {username,password} = req.body;
    console.log('/signup->req.body.username:'+username);
    db.collection("users").findOne({username:username, password : password},(err,user)=>{
            if(user){
                res.send("Welcome!")
            }
            else{
                res.send("Sorry, user does not exist.")
            }
        });
})

app.get('/echo.php',function (req,res){
    var data= req.query.data
    res.send(data)
})

app.post('/echo.php',function (req,res){
    var data= req.body['data']
    res.send(data)
})
let fields={_id:false,
    zips:true,
    city:true,
    state_id:true,
    state_name:true,
    county_name:true,
    timezone:true
}
app.get('/uscities-search/:zips(\\d{1,5})',function (req,res){
    const db = dbClient.db();
    let zipRegExp = new RegExp(req.params.zips);
    const cursor = db.collection("uscities").find({zips:zipRegExp}).project(fields);
    cursor.toArray(function(err, results){
        console.log(results)
        res.send(results)
    })
})
app.get('/uscities-search/:city',function (req,res){
    const db = dbClient.db();
    let cityRegExp = new RegExp(req.params.city,'i');
    const cursor = db.collection("uscities").find({city:cityRegExp}).project(fields);
    cursor.toArray(function(err, results){
        console.log(results)
        res.send(results)
    })})
