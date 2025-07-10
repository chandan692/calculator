# calculator
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Simple Calculator</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css"
  />
  <style>
    input[type="button"] {
      -webkit-appearance: none;
      -moz-appearance: none;
      appearance: none;
    }
  </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">
  <div class="bg-white rounded-lg shadow-lg w-full max-w-md p-6">
    <h1 class="text-3xl font-semibold text-center mb-6 text-gray-800">Simple Calculator</h1>
    <input
      type="text"
      id="display"
      class="w-full mb-6 text-right text-3xl font-mono p-3 rounded border border-gray-300 focus:outline-none focus:ring-2 focus:ring-blue-500"
      readonly
      aria-label="Calculator display"
      placeholder="0"
    />
    <div class="grid grid-cols-4 gap-4">
      <input type="button" value="7" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="8" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="9" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="÷" class="btn-operator bg-blue-500 hover:bg-blue-600 text-white rounded text-2xl font-semibold py-3" aria-label="Divide" />
      
      <input type="button" value="4" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="5" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="6" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="×" class="btn-operator bg-blue-500 hover:bg-blue-600 text-white rounded text-2xl font-semibold py-3" aria-label="Multiply" />
      
      <input type="button" value="1" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="2" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="3" class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="−" class="btn-operator bg-blue-500 hover:bg-blue-600 text-white rounded text-2xl font-semibold py-3" aria-label="Subtract" />
      
      <input type="button" value="0" class="btn-number col-span-2 bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" />
      <input type="button" value="." class="btn-number bg-gray-200 hover:bg-gray-300 text-gray-800 rounded text-2xl font-semibold py-3" aria-label="Decimal point" />
      <input type="button" value="+" class="btn-operator bg-blue-500 hover:bg-blue-600 text-white rounded text-2xl font-semibold py-3" aria-label="Add" />
      
      <input type="button" value="C" id="clear" class="bg-red-500 hover:bg-red-600 text-white rounded col-span-2 text-2xl font-semibold py-3" aria-label="Clear" />
      <input type="button" value="=" id="equals" class="bg-green-500 hover:bg-green-600 text-white rounded col-span-2 text-2xl font-semibold py-3" aria-label="Equals" />
    </div>
  </div>

  <script>
    const display = document.getElementById('display');
    const buttons = document.querySelectorAll('input[type="button"]');
    let currentInput = '';
    let lastInput = '';
    let resetNext = false;

    function isOperator(char) {
      return ['+', '−', '×', '÷'].includes(char);
    }

    function replaceOperators(str) {
      return str.replace(/×/g, '*').replace(/÷/g, '/').replace(/−/g, '-');
    }

    buttons.forEach(button => {
      button.addEventListener('click', () => {
        const val = button.value;

        if (val === 'C') {
          currentInput = '';
          display.value = '';
          resetNext = false;
          return;
        }

        if (val === '=') {
          if (currentInput === '') return;
          try {
            let expression = replaceOperators(currentInput);
            // Prevent eval injection by allowing only numbers, operators, and dots
            if (!/^[0-9+\-*/.() ]+$/.test(expression)) {
              display.value = 'Error';
              currentInput = '';
              return;
            }
            let result = Function('"use strict";return (' + expression + ')')();
            if (result === Infinity || result === -Infinity) {
              display.value = 'Error';
              currentInput = '';
              return;
            }
            if (Number.isNaN(result)) {
              display.value = 'Error';
              currentInput = '';
              return;
            }
            display.value = result;
            currentInput = result.toString();
            resetNext = true;
          } catch {
            display.value = 'Error';
            currentInput = '';
          }
          return;
        }

        if (resetNext) {
          if (isOperator(val)) {
            resetNext = false;
            currentInput += val;
            display.value = currentInput;
          } else {
            currentInput = val === '.' ? '0.' : val;
            display.value = currentInput;
            resetNext = false;
          }
          return;
        }

        if (isOperator(val)) {
          if (currentInput === '') {
            if (val === '−') {
              currentInput = val;
              display.value = currentInput;
            }
            return;
          }
          if (isOperator(currentInput.slice(-1))) {
            currentInput = currentInput.slice(0, -1) + val;
          } else {
            currentInput += val;
          }
          display.value = currentInput;
          return;
        }

        if (val === '.') {
          // Prevent multiple decimals in the current number
          let parts = currentInput.split(/[\+\−\×\÷]/);
          let lastPart = parts[parts.length - 1];
          if (lastPart.includes('.')) return;
          if (lastPart === '') {
            currentInput += '0.';
          } else {
            currentInput += '.';
          }
          display.value = currentInput;
          return;
        }

        // Number input
        currentInput += val;
        display.value = currentInput;
      });
    });
  </script>
</body>
</html>
