
{% assign BITS = 24 %}
<style>
    td {
        text-align: center;
        vertical-align: middle;
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
                <img id="bulb{{ i }}" src="{{site.baseurl}}/images/bulb_off.png" alt="" width="40" height="Auto">
                <div class="button" id="butt{{ i }}" onclick="toggleBit({{ i }})">on</div>
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
<div id="color-display" style="width: 100px; height: 100px; margin-top: 10px;"></div>
<div>
    <button onclick="invertBits()">Invert Bits</button>
</div>
<script>
    const BITS = {{ BITS }};
    const MAX = 2 ** BITS - 1;
    const IMAGE_ON = "{{site.baseurl}}/images/bulb_on.gif";
    const IMAGE_OFF = "{{site.baseurl}}/images/bulb_off.png";
    // return string with the current value of each bit
    function getBits() {
        let bits = "";
        for(let i = 0; i < BITS; i++) {
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
        } else {
            dig.value = 1;
            image.src = IMAGE_ON;
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
    // Update color display
    function updateColorDisplay() {
        const binary = getBits();
        const red = parseInt(binary.substring(0, 8), 2);
        const green = parseInt(binary.substring(8, 16), 2);
        const blue = parseInt(binary.substring(16, 24), 2);
        const colorDisplay = document.getElementById('color-display');
        colorDisplay.style.backgroundColor = `rgb(${red}, ${green}, ${blue})`;
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
    }
    // Call the initial update functions
    updateAsciiCharacter();
    updateColorDisplay();
</script>