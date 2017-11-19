# HCTF2017-writeups-eng
Translations of HCTF writeups to english.

## WEB:


### babycrack

Reference: https://github.com/LoRexxar/HCTF2017-babycrack

"The challenge I think is quite clear, you could even say that this kind of js
is basically the most common.

Obfuscation and Anti-Debugging

Here's a simple strategy for solving:

Patch to remove the anti-debugging code or debug using node -> beautify code ->
remove obfuscation -> analyze the logic of the code. The first part was flawed
(leading to multiple solutions) which was dealt with thru the hint, and when the
rest of the parts are complete you can use the sha256 hash to brute force. The
second part has three subsections, each with its own restrictions. The third
part and the fourth parts are very simple. I set the fifth part to have more
complicated logic, with the length and `ee` in the center, this part has only
one solution!

