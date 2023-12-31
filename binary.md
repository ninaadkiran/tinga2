{% assign BITS = 24 %}
<style>
    body {
        background-image: url('images/sunset.gif');
        background-size: cover;
        background-position: center;
        margin: 0;
        padding: 0;
        height: 160vh;
        color: grey;
    }

    td {
        text-align: center;
        vertical-align: middle;
        color: white;
    }

    #opacity-slider {
        width: 100%;
    }

    table {
        background-color: rgba(255, 255, 255, 0.8);
    }

    #binary {
        color: grey;
    }

    /* Updated button styles */
    .button {
        padding: 8px 16px; /* Adjust padding for a smaller size */
        background-color: purple; /* Purple background color */
        color: white;
        border: none;
        border-radius: 4px; /* Add rounded corners */
        cursor: pointer;
        margin: 5px;
    }

    button {
        padding: 8px 16px; /* Adjust padding for a smaller size */
        background-color: purple; /* Purple background color */
        color: white;
        border: none;
        border-radius: 4px; /* Add rounded corners */
        cursor: pointer;
        margin: 5px;
    }
</style>


<style>
    td {
        text-align: center;
        vertical-align: middle;
    }

    #opacity-slider {
        width: 100%;
    }
</style>
<table>
    <thead>
        <tr class="" id="">
            <th></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td id="binary">000000000000000000000000</td>
        </tr>
    </tbody>
</table>
{% comment %}
Liquid for loop includes the last number, thus the Minus
{% endcomment %}
{% assign bits = BITS | minus: 1 %}
<table>
    <thead>
        <tr>
            {% comment %}
            Build many bits
            {% endcomment %}
            {% for i in (0..bits) %}
            <th>
                <img id="bulb{{ i }}" src="{{site.baseurl}}/images/lightoff.jpg" alt="" width="40" height="Auto">
                <div class="button" id="butt{{ i }}" style="background-color:purple" onclick="toggleBit({{ i }})">off</div>
            </th>
            {% endfor %}
        </tr>
    </thead>
    <tbody>
        <tr>
            {% comment %}
            Value of bit
            {% endcomment %}
            {% for i in (0..bits) %}
            <td>
                <input type='text' id="digit{{ i }}" value="0" size="2" readonly>
            </td>
            {% endfor %}
        </tr>
    </tbody>
</table>
<div>
    <label for="resultant">Resultant:</label>
    <span id="resultant-value">0</span>
</div>
<div>
    <label for="ascii-character">ASCII Character:</label>
    <span id="ascii-character-value"></span>
</div>
<div id="color-display" style="width: 100px; height: 100px; margin-top: 10px; position: relative;">
    <input type="range" id="opacity-slider" min="0" max="1" step="0.01" value="1" oninput="updateColorDisplay()">
</div>
<div>
    <button onclick="invertBits()">Invert Bits</button>
</div>
<div>
    <button onclick="rotateBitsLeft()">Rotate Left</button>
</div>
<div>
    <button onclick="clearAll()">Clear All</button>
</div>
<div>
    <label for="toggle-gif-btn">Toggle Background GIF:</label>
    <button id="toggle-gif-btn" onclick="toggleBackgroundGif()">Toggle</button>
</div>
<div id="rgb-values">
    <label for="red-value">Red:</label>
    <span id="red-value">0</span>
    <br>
    <label for="green-value">Green:</label>
    <span id="green-value">0</span>
    <br>
    <label for="blue-value">Blue:</label>
    <span id="blue-value">0</span>
</div>
<div>
    <label for="color-picker">Color Picker:</label>
    <input type="color" id="color-picker" onchange="updateColorDisplay(); updateBinaryFromColor();">
</div>
<div>
    <label for="hex-color-code">Hex Color Code:</label>
    <span id="hex-color-code-value"></span>
</div>
<div>
    <label for="hex-to-rgb-input">Hex to RGB:</label>
    <input type="text" id="hex-to-rgb-input" oninput="updateRGBFromHex()">
</div>

<script>
    const BITS = {{ BITS }};
    const MAX = 2 ** BITS - 1;
    const IMAGE_ON = "{{site.baseurl}}/images/lighton.jpg";
    const IMAGE_OFF = "{{site.baseurl}}/images/lightoff.jpg";
    // return string with the current value of each bit
    function getBits() {
        let bits = "";
        for (let i = 0; i < BITS; i++) {
            bits = bits + document.getElementById('digit' + i).value;
        }
        return bits;
    }
    // setter for Document Object Model (DOM) values
    function setConversions(binary) {
        document.getElementById('binary').innerHTML = binary;
        // Resultant value
        document.getElementById('resultant-value').innerHTML = parseInt(binary, 2);
        // Update ASCII character display
        updateAsciiCharacter();
        // Update color display
        updateColorDisplay();
    }
    // toggle selected bit and recalculate
    function toggleBit(i) {
        const dig = document.getElementById('digit' + i);
        const image = document.getElementById('bulb' + i);
        const butt = document.getElementById('butt' + i);
        // Change digit and visual
        if (image.src.match(IMAGE_ON)) {
            dig.value = 0;
            image.src = IMAGE_OFF;
            butt.innerHTML = 'off'; // Update button text to 'off'
        } else {
            dig.value = 1;
            image.src = IMAGE_ON;
            butt.innerHTML = 'on'; // Update button text to 'on'
        }
        // Binary numbers
        const binary = getBits();
        setConversions(binary);
    }
    // add is a positive integer, subtract is a negative integer
    function add(n) {
        let binary = getBits();
        // convert to decimal and do math
        let decimal = parseInt(binary, 2);
        if (n > 0) {  // PLUS
            decimal = MAX === decimal ? 0 : decimal += n; // OVERFLOW or PLUS
        } else  {     // MINUS
            decimal = 0 === decimal ? MAX : decimal += n; // OVERFLOW or MINUS
        }
        // convert the result back to binary
        binary = decimal.toString(2).padStart(BITS, '0');
        // update conversions
        setConversions(binary);
        // update bits
        for (let i = 0; i < BITS; i++) {
            let digit = binary.charAt(i);
            document.getElementById('digit' + i).value = digit;
            document.getElementById('bulb' + i).src = digit === "1" ? IMAGE_ON : IMAGE_OFF;
        }
    }
    function updateColorDisplay() {
    const binary = getBits();
    const red = parseInt(binary.substring(0, 8), 2);
    const green = parseInt(binary.substring(8, 16), 2);
    const blue = parseInt(binary.substring(16, 24), 2);
    const opacity = document.getElementById('opacity-slider').value;
    const colorDisplay = document.getElementById('color-display');
    colorDisplay.style.backgroundColor = `rgba(${red}, ${green}, ${blue}, ${opacity})`;

    // Update hexadecimal color code
    const hexColorCode = rgbToHex(red, green, blue);
    document.getElementById('hex-color-code-value').innerHTML = hexColorCode;

    // Update RGB values
    document.getElementById('red-value').innerHTML = red;
    document.getElementById('green-value').innerHTML = green;
    document.getElementById('blue-value').innerHTML = blue;

    // Update buttons based on the binary representation of the color
    for (let i = 0; i < BITS; i++) {
        const digit = binary.charAt(i);
        const image = document.getElementById('bulb' + i);
        const butt = document.getElementById('butt' + i);

        if (digit === "1") {
            image.src = IMAGE_ON;
            butt.innerHTML = 'on';
        } else {
            image.src = IMAGE_OFF;
            butt.innerHTML = 'off';
        }
    }
}
    // Update ASCII character display
    function updateAsciiCharacter() {
        const binary = getBits();
        const decimal = parseInt(binary, 2);
        const asciiCharacter = String.fromCharCode(decimal);
        const asciiDisplay = document.getElementById('ascii-character-value');
        if (decimal >= 32 && decimal <= 126) {
            // Display printable ASCII characters
            asciiDisplay.innerHTML = asciiCharacter;
        } else {
            // Display non-printable ASCII characters
            asciiDisplay.innerHTML = "Non-Printable";
        }
    }
    // Invert all bits in the binary representation
    function invertBits() {
        let binary = getBits();
        binary = binary.split('').map(bit => (bit === '0' ? '1' : '0')).join('');
        setConversions(binary);
        // Update bits
        for (let i = 0; i < BITS; i++) {
            let digit = binary.charAt(i);
            document.getElementById('digit' + i).value = digit;
            document.getElementById('bulb' + i).src = digit === "1" ? IMAGE_ON : IMAGE_OFF;
        }
        // Reset opacity to 1 when inverting bits
        document.getElementById('opacity-slider').value = 1;
        updateColorDisplay();
    }
    // Rotate bits to the left
    function rotateBitsLeft() {
        let binary = getBits();
        binary = binary.substring(1) + binary[0];
        setConversions(binary);
        // Update bits
        for (let i = 0; i < BITS; i++) {
            let digit = binary.charAt(i);
            document.getElementById('digit' + i).value = digit;
            document.getElementById('bulb' + i).src = digit === "1" ? IMAGE_ON : IMAGE_OFF;
        }
    }
    // Clear all numbers and set them to 0
    function clearAll() {
        for (let i = 0; i < BITS; i++) {
            document.getElementById('digit' + i).value = 0;
            document.getElementById('bulb' + i).src = IMAGE_OFF;
        }
        // Set binary representation to all zeros
        const binary = '0'.repeat(BITS);
        setConversions(binary);
    }
    // Call the initial update functions
    updateAsciiCharacter();
    updateColorDisplay();
    // Initialize binary value to all zeros
    const initialBinary = '0'.repeat(BITS);
    setConversions(initialBinary);

    // Update binary from color picker
    function updateBinaryFromColor() {
        const colorPicker = document.getElementById('color-picker');
        const color = colorPicker.value;
        const rgb = hexToRgb(color.substring(1)); // Exclude the '#' from the hex color
        const binary = rgbToBinary(rgb);
        setConversions(binary);
        // Update bits
        for (let i = 0; i < BITS; i++) {
            let digit = binary.charAt(i);
            document.getElementById('digit' + i).value = digit;
            document.getElementById('bulb' + i).src = digit === "1" ? IMAGE_ON : IMAGE_OFF;
        }
        updateColorDisplay();
    }

    // Update RGB from Hex input
    function updateRGBFromHex() {
        const hexInput = document.getElementById('hex-to-rgb-input').value;
        const rgb = hexToRgb(hexInput);
        if (rgb) {
            const binary = rgbToBinary(rgb);
            setConversions(binary);
            // Update bits
            for (let i = 0; i < BITS; i++) {
                let digit = binary.charAt(i);
                document.getElementById('digit' + i).value = digit;
                document.getElementById('bulb' + i).src = digit === "1" ? IMAGE_ON : IMAGE_OFF;
            }
            updateColorDisplay();
        } else {
            // Handle invalid hex input
            alert("Invalid Hex Color Code");
        }
    }

    // Toggle background GIF
    function toggleBackgroundGif() {
        const body = document.body;
        const currentBackground = body.style.backgroundImage;
        if (currentBackground === 'url("images/sunset.gif")') {
            // Turn off the background GIF
            body.style.backgroundImage = 'none';
        } else {
            // Turn on the background GIF
            body.style.backgroundImage = 'url("images/sunset.gif")';
        }
    }

    // Helper function to convert RGB to binary
    function rgbToBinary(rgb) {
        const binaryRed = rgbComponentToBinary(rgb.r);
        const binaryGreen = rgbComponentToBinary(rgb.g);
        const binaryBlue = rgbComponentToBinary(rgb.b);
        return binaryRed + binaryGreen + binaryBlue;
    }

    // Helper function to convert a single RGB component to binary
    function rgbComponentToBinary(component) {
        return component.toString(2).padStart(8, '0');
    }

    // Helper function to convert hex color to RGB
    function hexToRgb(hex) {
        const bigint = parseInt(hex, 16);
        if (!isNaN(bigint) && bigint >= 0 && bigint <= 0xFFFFFF) {
            const r = (bigint >> 16) & 255;
            const g = (bigint >> 8) & 255;
            const b = bigint & 255;
            return { r, g, b };
        } else {
            return null; // Invalid hex value
        }
    }

    // Helper function to convert RGB to hexadecimal
    function rgbToHex(r, g, b) {
        const componentToHex = (c) => {
            const hex = c.toString(16);
            return hex.length == 1 ? '0' + hex : hex;
        };
        return '#' + componentToHex(r) + componentToHex(g) + componentToHex(b);
    }
</script>
