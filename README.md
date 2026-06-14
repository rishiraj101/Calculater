# Calculater
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Modern Calculator</title>
    <style>
        :root {
            --bg-color: #1e1e2e;
            --calc-bg: #11111b;
            --display-bg: #313244;
            --text-main: #cdd6f4;
            --btn-num: #45475a;
            --btn-op: #fab387;
            --btn-clear: #f38ba8;
            --btn-equal: #a6e3a1;
            --btn-hover: #585b70;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .calculator {
            background-color: var(--calc-bg);
            padding: 24px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            width: 340px;
        }

        .display-container {
            background-color: var(--display-bg);
            padding: 16px;
            border-radius: 12px;
            margin-bottom: 20px;
            text-align: right;
            min-height: 90px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            word-break: break-all;
        }

        .expression {
            color: #89b4fa;
            font-size: 0.95rem;
            min-height: 1.2rem;
        }

        .result {
            color: var(--text-main);
            font-size: 2rem;
            font-weight: 600;
        }

        .buttons-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
        }

        button {
            border: none;
            padding: 18px;
            font-size: 1.25rem;
            font-weight: 600;
            border-radius: 10px;
            cursor: pointer;
            transition: background-color 0.15s ease, transform 0.05s ease;
            color: var(--text-main);
            background-color: var(--btn-num);
        }

        button:active {
            transform: scale(0.95);
        }

        button:hover {
            filter: brightness(1.2);
        }

        button.operator {
            background-color: var(--btn-op);
            color: #11111b;
        }

        button.clear {
            background-color: var(--btn-clear);
            color: #11111b;
        }

        button.equal {
            background-color: var(--btn-equal);
            color: #11111b;
            grid-column: span 2;
        }
    </style>
</head>
<body>

    <div class="calculator">
        <div class="display-container">
            <div class="expression" id="expression"></div>
            <div class="result" id="result">0</div>
        </div>
        <div class="buttons-grid">
            <button class="clear" onclick="clearScreen()">C</button>
            <button onclick="deleteLast()">⌫</button>
            <button class="operator" onclick="appendOperator('/')">÷</button>
            <button class="operator" onclick="appendOperator('*')">×</button>
            
            <button onclick="appendNumber('7')">7</button>
            <button onclick="appendNumber('8')">8</button>
            <button onclick="appendNumber('9')">9</button>
            <button class="operator" onclick="appendOperator('-')">−</button>
            
            <button onclick="appendNumber('4')">4</button>
            <button onclick="appendNumber('5')">5</button>
            <button onclick="appendNumber('6')">6</button>
            <button class="operator" onclick="appendOperator('+')">+</button>
            
            <button onclick="appendNumber('1')">1</button>
            <button onclick="appendNumber('2')">2</button>
            <button onclick="appendNumber('3')">3</button>
            <button class="equal" onclick="calculate()">=</button>
            
            <button style="grid-column: span 2;" onclick="appendNumber('0')">0</button>
            <button onclick="appendNumber('.')">.</button>
        </div>
    </div>

    <script>
        const expressionDisplay = document.getElementById('expression');
        const resultDisplay = document.getElementById('result');
        
        let currentInput = '';
        let shouldResetDisplay = false;

        function appendNumber(num) {
            if (shouldResetDisplay) {
                currentInput = '';
                shouldResetDisplay = false;
            }
            
            if (num === '.' && currentInput.split(/[\+\-\*\/]/).pop().includes('.')) return;
            
            currentInput += num;
            updateDisplay();
        }

        function appendOperator(op) {
            if (currentInput === '' && op === '-') {
                currentInput += op;
                updateDisplay();
                return;
            }
            if (currentInput === '' || /[\+\-\*\/]$/.test(currentInput)) {
                // Replace the last operator if clicked a different one
                if(/[\+\-\*\/]$/.test(currentInput)) {
                    currentInput = currentInput.slice(0, -1) + op;
                    updateDisplay();
                }
                return;
            }
            shouldResetDisplay = false;
            currentInput += op;
            updateDisplay();
        }

        function clearScreen() {
            currentInput = '';
            expressionDisplay.innerText = '';
            resultDisplay.innerText = '0';
        }

        function deleteLast() {
            currentInput = currentInput.toString().slice(0, -1);
            updateDisplay();
        }

        function updateDisplay() {
          
            let formattedDisplay = currentInput
                .replace(/\*/g, ' × ')
                .replace(/\//g, ' ÷ ')
                .replace(/\+/g, ' + ')
                .replace(/\-/g, ' − ');
            
            resultDisplay.innerText = formattedDisplay || '0';
            
            if (currentInput && !/[\+\-\*\/]$/.test(currentInput)) {
                try {
                    
                    let preview = Function('"use strict";return (' + currentInput + ')')();
                    expressionDisplay.innerText = preview;
                } catch (e) {
                    
                }
            } else {
                expressionDisplay.innerText = '';
            }
        }

        function calculate() {
            if (!currentInput || /[\+\-\*\/]$/.test(currentInput)) return;
            
            try {
                let finalResult = Function('"use strict";return (' + currentInput + ')')();
                

                if (!Number.isInteger(finalResult)) {
                    finalResult = parseFloat(finalResult.toFixed(8));
                }
                
                expressionDisplay.innerText = currentInput.replace(/\*/g, '×').replace(/\//g, '÷') + ' =';
                resultDisplay.innerText = finalResult;
                currentInput = finalResult.toString();
                shouldResetDisplay = true;
            } catch (error) {
                resultDisplay.innerText = 'Error';
                currentInput = '';
            }
        }

      
        document.addEventListener('keydown', (event) => {
            const key = event.key;
            
            if (/[0-9.]/.test(key)) {
                appendNumber(key);
            } else if (['+', '-', '*', '/'].includes(key)) {
                appendOperator(key);
            } else if (key === 'Enter' || key === '=') {
                event.preventDefault();
                calculate();
            } else if (key === 'Backspace') {
                deleteLast();
            } else if (key === 'Escape') {
                clearScreen();
            }
        });
    </script>
</body>
</html>
