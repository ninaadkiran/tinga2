---
title: 2.1 Binary
layout: post
courses: { compsci: {week: 5} }
type: hacks
---

<!-- 

Learn how page works, plus learn about binary
Hack 0: Do your own on/off thing with Image and Buttons thing
Hack 1: change display to indicate the value of bin (128, 64, 32, 16, 8, 4, 2, 1)
Hack 2: change one-zero input under bulb to perform updates to the page

Learn about binary representations
Hack 3: add an ASCII character display to text when 8 bits, determine if printable or not printable
Hack 4: change to 24 bits and add a color code and display color when 24 bits. Think about display on this one, perhaps wrap bits 

Jekyll Table Reference: https://idratherbewriting.com/documentation-theme-jekyll/mydoc_tables.html

--->

{% assign BITS = 8 %}

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
            <td id="binary">00000000</td>
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

<script>
    const BITS = {{ BITS }};
    const MAX = 2 ** BITS - 1;
    const IMAGE_ON = "{{site.baseurl}}/images/bulb_on.gif";
    const IMAGE_OFF = "{{site.baseurl}}/images/bulb_off.png"

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
</script>
