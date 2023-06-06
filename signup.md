<head>
    <link rel="stylesheet" href="signup.scss">
    <title>Geotourism - Sign Up</title>
</head>
<body>
    <section>
        <div class="signin-box">
            <div class="signin-value">
                <form action="">
                    <h2>Sign Up</h2>
                    <div class="inputbox">
                        <ion-icon name="mail-outline"></ion-icon>
                        <input type="email" required>
                        <label for="">Username</label>
                    </div>
                    <div class="inputbox">
                        <ion-icon name="lock-closed-outline"></ion-icon>
                        <input type="password" required>
                        <label for="">Password</label>
                    </div>
                    <button onclick="goBack()">Sign Up</button>
                </form>
            </div>
        </div>
    </section>
    <script>
        function goBack() {
            window.location.href = "https://a1234l.github.io/Final-CSP-Project/index";
        }
    </script>
    <script type="module" src="https://unpkg.com/ionicons@7.1.0/dist/ionicons/ionicons.esm.js"></script>
    <script nomodule src="https://unpkg.com/ionicons@7.1.0/dist/ionicons/ionicons.js"></script>
</body>
