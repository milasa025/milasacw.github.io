---
permalink: /
title: "Academic Pages is a ready-to-fork GitHub Pages template for academic personal websites"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mobile Quiz App</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Arial, sans-serif;
        }
        
        body {
            background-color: #f5f5f5;
            color: #333;
            line-height: 1.6;
        }
        
        .container {
            max-width: 100%;
            padding: 20px;
            margin: 0 auto;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }
        
        .quiz-container {
            background-color: white;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
            padding: 24px;
            margin-bottom: 20px;
        }
        
        .question {
            font-size: 1.3rem;
            font-weight: bold;
            margin-bottom: 24px;
            color: #2c3e50;
        }
        
        .options {
            display: grid;
            gap: 12px;
        }
        
        .option {
            background-color: #f8f9fa;
            border: 2px solid #e9ecef;
            border-radius: 8px;
            padding: 16px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: 500;
        }
        
        .option:hover, .option:focus {
            background-color: #e9ecef;
            border-color: #ced4da;
        }
        
        .option.selected {
            background-color: #4299e1;
            color: white;
            border-color: #2b6cb0;
        }
        
        .message-screen {
            display: none;
            text-align: center;
            padding: 30px 20px;
            background-color: white;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        }
        
        .message-title {
            font-size: 1.5rem;
            color: #2c3e50;
            margin-bottom: 16px;
        }
        
        .message-body {
            font-size: 1.1rem;
            margin-bottom: 24px;
        }
        
        .message-location {
            font-size: 1.3rem;
            font-weight: bold;
            color: #e74c3c;
            margin-bottom: 24px;
        }
        
        .reset-btn {
            background-color: #4299e1;
            color: white;
            border: none;
            border-radius: 8px;
            padding: 12px 24px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }
        
        .reset-btn:hover, .reset-btn:focus {
            background-color: #3182ce;
        }
        
        .result-icon {
            font-size: 3rem;
            margin-bottom: 16px;
        }
        
        .correct-icon {
            color: #48bb78;
        }
        
        .incorrect-icon {
            color: #f56565;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="quiz-container" id="quiz-container">
            <div class="question" id="question">Which animal has not appeared on campus before?</div>
            <div class="options" id="options">
                <div class="option" onclick="selectOption(0)">Sun Conure Parrot</div>
                <div class="option" onclick="selectOption(1)">Mallard</div>
                <div class="option" onclick="selectOption(2)">Wood Pigeon</div>
                <div class="option" onclick="selectOption(3)">Canada Goose</div>
            </div>
        </div>
        
        <div class="message-screen" id="correct-screen">
            <div class="result-icon correct-icon">✓</div>
            <div class="message-title">Correct Answer!</div>
            <div class="message-body">Great job!</div>
            <div class="message-body">Next location:</div>
            <div class="message-location" id="correct-location">Location A</div>
<!--            <button class="reset-btn" onclick="resetQuiz()">Try Again</button>-->
        </div>
        
        <div class="message-screen" id="incorrect-screen">
            <div class="result-icon incorrect-icon">✗</div>
            <div class="message-title">Incorrect Answer</div>
<!--            <div class="message-body">Try again!</div>-->
            <div class="message-body">Next location:</div>
            <div class="message-location" id="incorrect-location">Location B</div>
<!--            <button class="reset-btn" onclick="resetQuiz()">Try Again</button>-->
        </div>
    </div>

    <script>
        // Single quiz question
        const quizQuestion = {
            question: "Which animal has not appeared on campus before?",
            options: ["Sun Conure Parrot", "Mallard", "Wood Pigeon", "Canada Goose"],
            correct: 0,
            correctLocation: "The table on the first floor of Si Yuan Centre",
            incorrectLocation: "The coffee table in front of Jubilee Conference Centre"
        };
        
        // DOM elements
        const quizContainer = document.getElementById("quiz-container");
        const correctScreen = document.getElementById("correct-screen");
        const incorrectScreen = document.getElementById("incorrect-screen");
        const questionElement = document.getElementById("question");
        const optionsContainer = document.getElementById("options");
        const correctLocationElement = document.getElementById("correct-location");
        const incorrectLocationElement = document.getElementById("incorrect-location");
        
        // Initialize the quiz
        function initQuiz() {
            // Set the question and location messages
            questionElement.textContent = quizQuestion.question;
            correctLocationElement.textContent = quizQuestion.correctLocation;
            incorrectLocationElement.textContent = quizQuestion.incorrectLocation;
            
            // Reset screens
            correctScreen.style.display = "none";
            incorrectScreen.style.display = "none";
            quizContainer.style.display = "block";
            
            // Populate options
            optionsContainer.innerHTML = "";
            quizQuestion.options.forEach((option, index) => {
                const optionElement = document.createElement("div");
                optionElement.className = "option";
                optionElement.textContent = option;
                optionElement.addEventListener("click", () => selectOption(index));
                optionsContainer.appendChild(optionElement);
            });
        }
        
        // Handle option selection
        function selectOption(index) {
            // Clear previously selected option
            const options = document.querySelectorAll(".option");
            options.forEach(option => option.classList.remove("selected"));
            
            // Mark selected option
            options[index].classList.add("selected");
            
            // Check answer after a short delay
            setTimeout(() => {
                checkAnswer(index);
            }, 500);
        }
        
        // Check if the selected answer is correct
        function checkAnswer(selectedIndex) {
            // Hide quiz container
            quizContainer.style.display = "none";
            
            // Show appropriate message screen
            if (selectedIndex === quizQuestion.correct) {
                correctScreen.style.display = "block";
            } else {
                incorrectScreen.style.display = "block";
            }
        }
        
        // Reset the quiz
        function resetQuiz() {
            // Hide message screens
            correctScreen.style.display = "none";
            incorrectScreen.style.display = "none";
            
            // Show quiz container
            quizContainer.style.display = "block";
            

            // Reset any selected options
            const options = document.querySelectorAll(".option");
            options.forEach(option => option.classList.remove("selected"));
        }
        
        // Initialize the quiz when page loads
        window.addEventListener("load", initQuiz);
    </script>
</body>
</html>
