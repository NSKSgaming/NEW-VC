<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Study App</title>
    <script src="https://kit.fontawesome.com/a076d05399.js" crossorigin="anonymous"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js"></script>
    <style>
        /* General Styles */
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: Arial, sans-serif; }
        body, html { height: 100%; background-color: #ffffff; overflow-x: hidden; }

        /* Logo Animation */
        .logo-container {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100%;
            width: 100%;
            background-color: #4CAF50;
            position: fixed;
            z-index: 9999;
            animation: fadeOut 1.5s ease-in-out 2.5s forwards;
        }

        .logo {
            width: 200px;
            height: 200px;
            background-image: url('https://i.postimg.cc/FRx0Cf9h/Picsart-24-12-23-17-15-51-010.png');
            background-size: cover;
            background-repeat: no-repeat;
            border-radius: 50%;
            animation: scaleIn 2s ease-in-out forwards;
        }

        @keyframes scaleIn {
            0% { transform: scale(0); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }

        @keyframes fadeOut {
            100% { opacity: 0; visibility: hidden; }
        }

        /* Header */
        header {
            background-color: #ff2e00;
            color: white;
            padding: 1rem;
            text-align: center;
            font-size: 1.8rem;
        }

        .header-title {
            font-size: 2rem;
            font-weight: bold;
            color: white;
            margin-top: 10px;
            text-align: center;
        }

        /* Grid Layout */
        .grid-container {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 1rem;
            padding: 1rem;
            margin: auto;
            max-width: 1200px;
        }

        .grid-item {
            background-color: white;
            padding: 1rem;
            text-align: center;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            transition: 0.3s;
        }

        .grid-item:hover {
            transform: translateY(-5px);
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
        }

        .grid-item i {
            font-size: 2.5rem;
            color: #ff2e00;
            margin-bottom: 0.5rem;
        }

        .grid-item h3 {
            font-size: 1.2rem;
            font-weight: bold;
            color: #333;
        }

        /* Footer Navigation */
        .navbar {
            display: flex;
            justify-content: space-around;
            position: fixed;
            bottom: 0;
            width: 100%;
            background: #fff;
            padding: 1rem;
            box-shadow: 0 -2px 6px rgba(0, 0, 0, 0.1);
            z-index: 1000;
        }

        .navbar a {
            text-decoration: none;
            color: #333;
            text-align: center;
            font-size: 1rem;
            transition: color 0.3s ease;
        }

        .navbar a:hover {
            color: #ff2e00;
        }

        .navbar i {
            font-size: 1.8rem;
            color: #ff2e00;
        }

        /* Button */
        .auth-btn {
            display: flex;
            justify-content: center;
            margin: 20px;
        }

        .auth-btn button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
            font-size: 1rem;
            border-radius: 5px;
        }

        .auth-btn button:hover {
            background-color: #388E3C;
        }
    </style>
</head>
<body>
    <!-- LOGO ANIMATION -->
    <div class="logo-container">
        <div class="logo"></div>
    </div>

    <!-- MAIN APP CONTENT -->
    <div class="main-content" style="display:none;">
        <header>
            <div class="header-title">NEW VECTOR CLASSES</div>
        </header>

        <div class="auth-btn">
            <button id="loginBtn">Login with Google</button>
        </div>

        <section class="grid-container" id="courses-container">
            <!-- Dynamic Course Data Will Appear Here -->
        </section>
    </div>

    <nav class="navbar">
        <a href="#"><i class="fas fa-home"></i><br>Home</a>
        <a href="#"><i class="fas fa-graduation-cap"></i><br>My Courses</a>
        <a href="#"><i class="fas fa-download"></i><br>Downloads</a>
        <a href="#"><i class="fas fa-comments"></i><br>Chat</a>
    </nav>

    <!-- Firebase Configuration & Scripts -->
    <script>
        // Firebase Configuration
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
            projectId: "YOUR_PROJECT_ID",
            storageBucket: "YOUR_PROJECT_ID.appspot.com",
            messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
            appId: "YOUR_APP_ID"
        };

        // Initialize Firebase
        const app = firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        // Authentication Logic
        document.getElementById('loginBtn').addEventListener('click', () => {
            const provider = new firebase.auth.GoogleAuthProvider();
            auth.signInWithPopup(provider)
                .then(result => {
                    console.log("User signed in:", result.user.displayName);
                    alert(`Welcome, ${result.user.displayName}`);
                })
                .catch(error => {
                    console.error("Auth Error:", error.message);
                });
        });

        // Fetch Courses from Firestore
        db.collection("courses").get().then((querySnapshot) => {
            const container = document.getElementById('courses-container');
            querySnapshot.forEach((doc) => {
                const data = doc.data();
                container.innerHTML += `
                    <div class="grid-item">
                        <i class="fas fa-book"></i>
                        <h3>${data.title}</h3>
                    </div>
                `;
            });
        });

        // Show Main Content after Animation
        document.addEventListener("DOMContentLoaded", () => {
            setTimeout(() => {
                document.querySelector('.main-content').style.display = "block";
            }, 2500);
        });
       
    </script>
</body>
</html>
