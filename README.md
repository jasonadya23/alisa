<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Birthday Card</title>
    <!-- Animation and Confetti Libraries -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/animejs/3.2.1/anime.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        :root {
            --bg-color: #f0f4f8;
            --card-bg: #ffffff;
            --text-color: #333333;
            --primary: #ff6b6b;
        }

        body {
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            transition: background-color 0.5s ease;
        }

        /* Container for the whole app */
        .app-container {
            width: 100%;
            max-width: 500px;
            padding: 20px;
            text-align: center;
            position: relative;
        }

        /* Input Form Styles */
        .form-container {
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            margin-bottom: 20px;
            z-index: 10;
            position: relative;
        }

        input, textarea, select, button {
            width: 100%;
            margin-bottom: 15px;
            padding: 12px;
            border: 2px solid #e1e8ed;
            border-radius: 8px;
            font-size: 16px;
            box-sizing: border-box;
            font-family: inherit;
        }

        button {
            background-color: var(--primary);
            color: white;
            border: none;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s, background-color 0.2s;
        }

        button:hover {
            background-color: #ff5252;
            transform: translateY(-2px);
        }

        /* The Gift Box */
        .gift-wrapper {
            display: none;
            cursor: pointer;
            position: relative;
            height: 200px;
            width: 200px;
            margin: 50px auto;
        }

        .gift-box {
            width: 150px;
            height: 150px;
            background-color: #4ecdc4;
            position: absolute;
            bottom: 0;
            left: 25px;
            border-radius: 10px;
            box-shadow: 0 10px 20px rgba(0,0,0,0.15);
        }

        .ribbon-vertical {
            position: absolute;
            width: 30px;
            height: 100%;
            background-color: #ffbe0b;
            left: 60px;
        }

        .ribbon-horizontal {
            position: absolute;
            width: 100%;
            height: 30px;
            background-color: #ffbe0b;
            top: 60px;
        }

        .gift-lid {
            width: 170px;
            height: 40px;
            background-color: #45b7af;
            position: absolute;
            bottom: 150px;
            left: 15px;
            border-radius: 5px;
            z-index: 2;
        }

        /* The Revealed Card */
        .card-container {
            display: none;
            background: var(--card-bg);
            color: var(--text-color);
            padding: 40px;
            border-radius: 20px;
            box-shadow: 0 15px 40px rgba(0,0,0,0.2);
            transform: scale(0.8);
            opacity: 0;
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
        }

        .card-container h1 {
            margin-top: 0;
            color: var(--primary);
        }

        .card-message {
            font-size: 1.2rem;
            line-height: 1.6;
            white-space: pre-wrap;
        }
    </style>
</head>
<body>

<div class="app-container">
    
    <!-- Step 1: Form to create the card -->
    <div class="form-container" id="setup-form">
        <h2>Create a Virtual Card</h2>
        <input type="text" id="recipient-name" placeholder="Recipient's Name" required>
        <textarea id="card-message-input" rows="4" placeholder="Write your birthday message here..." required></textarea>
        <select id="theme-selector">
            <option value="celebration">Celebration Theme</option>
            <option value="minimalist">Minimalist Theme</option>
            <option value="fun">Fun Theme</option>
        </select>
        <button onclick="packGift()">Pack the Gift!</button>
    </div>

    <!-- Step 2: The Clickable Gift -->
    <div class="gift-wrapper" id="gift" onclick="unwrapGift()">
        <div class="gift-box">
            <div class="ribbon-vertical"></div>
            <div class="ribbon-horizontal"></div>
        </div>
        <div class="gift-lid"></div>
    </div>

    <!-- Step 3: The Revealed Card -->
    <div class="card-container" id="final-card">
        <h1 id="display-name">Happy Birthday!</h1>
        <div class="card-message" id="display-message"></div>
        <br>
        <button onclick="location.reload()" style="width: 50%;">Make Another</button>
    </div>

</div>

<script>
    // Theme configurations
    const themes = {
        celebration: { bg: '#ffeaa7', card: '#ffffff', text: '#2d3436', primary: '#e84393' },
        minimalist: { bg: '#f4f4f4', card: '#ffffff', text: '#333333', primary: '#555555' },
        fun: { bg: '#a29bfe', card: '#fdcb6e', text: '#2d3436', primary: '#d63031' }
    };

    function packGift() {
        const name = document.getElementById('recipient-name').value;
        const message = document.getElementById('card-message-input').value;
        const theme = document.getElementById('theme-selector').value;

        if (!name || !message) {
            alert('Please enter a name and message!');
            return;
        }

        // Apply theme colors
        const root = document.documentElement;
        root.style.setProperty('--bg-color', themes[theme].bg);
        root.style.setProperty('--card-bg', themes[theme].card);
        root.style.setProperty('--text-color', themes[theme].text);
        root.style.setProperty('--primary', themes[theme].primary);

        // Populate card
        document.getElementById('display-name').innerText = `Happy Birthday, ${name}!`;
        document.getElementById('display-message').innerText = message;

        // Hide form, show gift
        document.getElementById('setup-form').style.display = 'none';
        
        const gift = document.getElementById('gift');
        gift.style.display = 'block';

        // Animate gift bouncing in
        anime({
            targets: '.gift-wrapper',
            translateY: [-200, 0],
            opacity: [0, 1],
            duration: 1000,
            elasticity: 600
        });
    }

    function unwrapGift() {
        // Pop the lid off
        anime({
            targets: '.gift-lid',
            translateY: -100,
            rotate: 15,
            opacity: 0,
            duration: 600,
            easing: 'easeOutExpo'
        });

        // Shrink and fade the box
        anime({
            targets: '.gift-box',
            scale: 0.8,
            opacity: 0,
            duration: 600,
            easing: 'easeOutExpo',
            complete: function() {
                document.getElementById('gift').style.display = 'none';
                showCard();
            }
        });

        // Trigger Confetti
        confetti({
            particleCount: 150,
            spread: 90,
            origin: { y: 0.6 },
            colors: ['#ffbe0b', '#fb5607', '#ff006e', '#8338ec', '#3a86ff']
        });
    }

    function showCard() {
        const card = document.getElementById('final-card');
        card.style.display = 'block';

        // Animate card appearing
        anime({
            targets: '.card-container',
            scale: [0.5, 1],
            opacity: [0, 1],
            duration: 800,
            elasticity: 400
        });
    }
</script>

</body>
</html>
