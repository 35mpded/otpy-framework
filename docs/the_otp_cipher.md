# THE CIPHER

A prime example of the One-Time Pad cipher in action is found in  [number stations](https://www.youtube.com/watch?v=IlpOMkuTwmU), known for their unique broadcasts of random number sequences over shortwave radio. These stations use an encryption techniques know as "One-Time Pad (OTP)" to send confidential message to intelligence operatives (aka spies) engaged in clandestine operations, and sometimes by military forces in active duty zones. This method of encryption has been in use for covert communication since before World War II and the Cold War era, as the One-Time Pad Cipher is unique in being the only information-theoretically secure cipher. It is simply unsolvable due to its mathematical properties, rendering it immune to any future advancements in mathematics or leaps in computer technology, such as computational speed or developments in quantum computing. This property of the cipher attributed to its lasting significance despite its seemingly antiquated nature.  

However, the cipher is unbreakable only if these rules are strictly followed:
-   The OTP keys must be truly random (cryptographically secure).
	<br>*Uniformly distributed and independent of the plaincode*
-   The one-time pad key must not be shorter than the plaincode.
-   Each ciphertext must be of the same predefined length and reasonably long (the longer the better).
	<br>*Note that you can change the length but should avoid doing so frequently.*
-   The OTP Keys must never be reused as a whole or in part.
-   The OTP Keys must be kept in complete secrecy.
-   After its use an OTP key must be immediately destroyed 

I'm sure that by now it seem very complicated, but it's actually quite simple - the whole encryption/decryption process can be broken down into a couple of steps, and by the end of this document you should have a solid understanding of the cipher.

**ENCRYPTION STEPS:**
 1. A plaintext (unencoded and unencrypted message) is encoded into plaincode with the use of a checkerboard. 
 2. The plaincode then is encrypted by using a cryptographic key (a one-time pad) and modular arithmetic.
 3. The used one-time pad key is then destroyed.

**DECRYPTION STEPS:**
 1. The cyphertext is decrypted to plaincode by using a cryptographic key (a one-time pad) and modular arithmetic.
 2. The plaincode is decoded into plaintext with the use of a checkerboard. 
 3. The used one-time pad key is then destroyed.

Don't worry if you're feeling overwhelmed; the mentioned steps will be thoroughly explained in the sections that follow.<br>You can also check this awesome video for visual demonstration: [The ULTIMATE One Time Pad Tutorial](https://www.youtube.com/watch?v=rCeWtQERizA&lc=UgxNO0gFvF-qLf0YiYF4AaABAg.9y0-NsbHT239yEB73sMyWK)

## ENCODING AND DECODING 

  
As outlined earlier, the initial step in using the cipher involves encoding the message (known as plaintext) into plaincode, which is essentially the process of translating letters into numbers ranging from 0 to 9. This conversion is achieved through the use of a tool akin to a cheatsheet known as a straddling checkerboard. The straddling checkerboard is essentially a grid system used to encode alphanumeric letters into numerical digits. The particular checkerboard that we'll use in this example is known as "AT ONE SIR" and is specifically optimized for English (by letter arrangement in the grid):


|   | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8   | 9   |
|---|---|---|---|---|---|---|---|---|-----|-----|
|   | A | T |   | O | N | E |   | S | I   | R   |
| 2 | B | C | D | F | G | H | J | K | L   | M   |
| 6 | P | Q | U | V | W | X | Y | Z | F/L | /   |

The above table is just one way to represent a checkerboard; there are alternative, potentially simpler ones. However, field operatives sometimes have to dispose of their checkerboards and then reconstruct them from memory. Remembering the sequence of letters through the mnemonic "AT ONE SIR" is more practical for this purpose than memorizing each letter's specific code. The rationale for presenting the earlier example was to illustrate this method, but for clarity and to enhance understanding, here's an alternative representation of the checkerboard:
|   | 0    | 1    | 2   | 3     | 4   | 5   | 6   | 7   | 8     | 9   |
|---|------|------|-----|-------|-----|-----|-----|-----|-------|-----|
|   | A:0  | T:1  |     | O:3   | N:4 | E:5 |     | S:7 | I:8   | R:9 |
| 2 | B:20 | C:21 | D:22| F:23  | G:24| H:25| J:26| K:27| L:28  | M:29|
| 6 | P:60 | Q:61 | U:62| V:63  | W:64| X:65| Y:66| Z:67| F/L:68| /:69|

### EXAMPLES
Start by breaking the words you want to encode into individual letters. For each letter, you have to find the corresponding row digit (leftmost numbers) and the column digit (topmost numbers). After both digits are found, you combine them to create the encoding number. If a number does not have a row digit, then you write only the column digit. Notice that fields under column 2 and 6 are empty. This is because digits 2 and 6 are used for identifying letters adjacent to them (i.e. the double-digit letters). This is actually how the "AT ONE SIR" checkerboard is optimized for English. The most frequently used letters are placed on the single-digit row. This, in turn, allows for the encryption of larger messages as the resulting plaincode is smaller.

Let's encode the letter 'H' using the checkerboard shown above.
 1. Identify the row digit for the letter 'H', the row digit is '2'
 2. Identify the column digit for the letter 'H"', the column digit is '5'. 
 3. Now combine the row and column digits to get the encoding number for letter 'H'. Your result should be '25'.

Let's now encode the letter 'E'
 1. Identify the row digit for the letter 'E', since there is no row digit you just ingore that.
 3. Identify the column digit for letter "E", this digit is '5'
 4. As there is no row digit to combine with the column digit, the encoding number for the letter 'E' is just '5'

Let's now encode the word 'HELLO'
```
H   E  L   L   O
25  5  28  28  3
```
If you have paid attention, you've probably noticed the designation 'F/L' on the checkerboard. This stands for FIGURE/LETTER and is used to switch between letters (A-Z) and numbers (0-9). Let's look at a more practical example by encoding the following sentence: 'MEET/ME/2DAY/AT/1130'
```
M  E E T /  M  E /    2   D  A Y  /  A  T /    1130
29 5 5 1 69 29 5 69 68268 22 0 66 69 0  1 69 68113068
```
You might want to split the plaincode into groups of five digits. This will make it nice to read and help the eyes track where you've reached during the encryption:
`29551 69295 69682 68220 66690 16968 11306 8` 

As you've probably guessed by now - the opposite transformation is achieved by converting numbers into letters:
```
29 5 5 1 69 29 5 69 68268 22 0 66 69 0  1 69 68113068
M  E E T /  M  E /    2   D  A Y  /  A  T /    1130
```
*Keep in mind that encoding is not an encryption, as such it does not provide any cryptographic security whatsoever! The encoding only prepares the plaintext for the actual encryption process. Therefore, the result of the encoding is called plaincode to stress that the message is still in its plain readable form! Here, only the checkerboard's efficiency matters as this type of encryption used later is unbreakable anyway, thus all variations of the straddling checkerboard are equally safe.*

## ENCRYPTION/DECRYPTION 
The One-Time Pad Cipher as a whole consists of the following elements:
- $P$- The encoded text called plaincode
- $K$- The cryptographic key called one-time pad (OTP).
- $C$- The ciphertext which is the result of the encryption, i.e. the encrypted message.

The whole encryption and decryption solution, is essentially a simple add or subtract operation performed on $P$ and $K$ by using modulus 10 arithmetic. The result of this calculation is the encrypted message (i.e. the cipher) - $C$. There are total of three schemes for the modulus 10 implementation of the OTP cipher:

**SCHEME 1**
<br>ENCRYPT: $(P-K)\ mod\ 10=C$
<br>DECRYPT: $P=(C+K)\ mod\ 10$

**SCHEME 2**
<br>ENCRYPT: $(P+K)\ mod\ 10=C$
<br>DECRYPT: $P=(C-K)\ mod\ 10$

**SCHEME 3**
<br>ENCRYPT: $(K-P)\ mod\ 10=C$
<br>DECRYPT: $P=(K-C)\ mod\ 10$

Often "scheme 1" is preferred for real use of the One-Time Pad Cipher, this is because addition is easier than subtraction. As you can probably imagine, field operatives are usually under pressure and at the receiving end of orders. Leaving the harder operation to people who'll frequently decrypt while under stress is not the most reasonable thing to do. However, all of the above are equally secure and acceptable to use, apart from the practical reason to pick one over the other.

### EXAMPLES
Please be aware of the following before you continue:
- The plaintext, otp key, and ciphertext are all written in groups of five digits. The grouping is done purely out of practicality, as it is easier to read and work with them.
- The initial set of five digits in the OTP key are never encrypted; those five digits are used as the OTP key identifier. This identifier is subsequently included to the beginning of the ciphertext. This enables the recipient to find the correct OTP key for decryption.
- Remember the rule that each ciphertext must be of the same predefined length? We'll use the length of 40 digits for the examples here, but in reality you might want to use something like 300 or more.
- Given that we'll use a 40 digit long key for the example (excluding the KEY ID) and that the our plaincode (from previous example) is only 36 digits long; we'll use random digit as padding to the end of the plaincode. However, remember that the OTP key must not be shorter than the plaincode. This is very important and you should never ignore it.

**ENCRYPTION**
<br>For encryption of the plaincode we'll use the formula $(P−K)\ mod\ 10 = C$:
```
Plaincode:  KEYID 29551 69295 69682 68220 66690 16968 11306 87534
OTP Key(-): 68496 37541 68327 10638 07532 29522 71162 68496 57318
----------------------------------------------------------------------------
Ciphertext: 68496 92010 01978 59054 61798 47178 45806 53910 30216 
```
You solve the equation by calculating the plaincode and one-time pad (digit by digit from left to right), using subtraction and modulo 10 arithmetic. This means you should calculate the digits similar to the normal subtraction $8 - 3 = 5$, with the exception that when the result is a negative number, you must add $10$ to the smaller number. 

E.g. since $5-10=-5$ you have to add 10 like so $(5 + 10 ) - 9 = 6$.

**DECRYPTION**
<br>For decryption of the ciphertext we'll use the formula $P = (C + K)\ mod\ 10$: 
```
Ciphertext: 68496 92010 01978 59054 61798 47178 45806 53910 30216
OTP Key(+): 68496 37541 68327 10638 07532 29522 71162 68496 57318
----------------------------------------------------------------------------
Plaincode:  KEYID 29551 69295 69682 68220 66690 16968 11306 87534
```

You solve the equation by calculating the ciphertext and one-time pad (digit by digit from left to right), using addition and modulo 10 arithmetic. This means you should calculate the digits similar to the normal addition $5+3 = 8$, with the exception that when the result is greater than $9$, you have to subtract $10$ from the end result.

E.g. since $7+9=16$ you have to subtract 10 like so $(7+9)-10 = 6$. 

## PRACTICE
Now that you know how the One-Time Pad Cipher works, it's time for some practice. Decrypt the cipher below by using the appropriate key and the "AT ONE SIR" checkerboard.

**MESSAGE**
```
Ciphertext: 65495 02432 03959 77254 80268 48619 34519 86403 93013
OTP Key(+): KEYID ????? ????? ????? ????? ????? ????? ????? ?????
----------------------------------------------------------------------------
Plaincode:  KEYID ????? ????? ????? ????? ????? ????? ????? ?????
```

**KEYS**
```
PAD 1: 65495 62196 25743 56202 18050 11300 18281 85107
PAD 2: 29525 44393 61446 99140 24935 68484 42359 53489
PAD 3: 65617 60328 84045 03766 12472 36570 19098 53165
```
Well done if you decrypted the message!

## Q&A

**Where can I get true random numbers?**
<br>Let's get something straight, true randomness is hardly possible. True randomness implies nondeterminism. If something can be predicted then it's deterministic and not so random. Even the universe is sometimes viewed as deterministic, but even deterministic systems can behave in unpredictable ways, making long-term predictions practically impossible despite the underlying determinism. So don't waste your time in search of true randomness, you need something which is nondeterministic enough to be practically impossible to determine. In cryptography this is called "Cryptographically Secure Random Number Generator". 
- The  [one-time pad python framework](https://github.com/SubXi/otpy-framework2) has proper implementation of CSRNG by using the python's `secrets` module. This module generates random numbers based on noise from the user's computer activity and hardware components.
- [Numbers 9.0](https://ciphermachinesandcryptology.com/en/numbersgen.htm) has an interesting implementation of CSRNG. It uses a large set of audio, video and image files to generate random numbers. 
- [Random.org](https://www.random.org/) uses atmospheric noise to generate random numbers, which theoretically should outperform other CSRNG methods. However, I have reservations about its reliability since the numbers are transmitted via the internet.

**Is the One-Time Pad Cipher really unbreakable?** 
<br>Yes! if truly random or at least cryptographically secure random numbers are used, and the above mentioned rules are followed, then the cipher is indeed unbreakable. In fact, it is the only practical cipher that is secure in an information-theoretic sense, also known as unconditional security. In most simplest terms, the reason for this is that the One-Time Pad Cipher is an equation with two unknowns, $P$ and $K$. Cryptologists can use various statistical and mathematical techniques to guess or estimate those unknowns to attack the ciphertext. However, the One-Time Pad Cipher uses modular arithmetic (specifically, modulus 10) to render such techniques ineffective. These techniques are rendered useless since, when mod 10 is applied, any possible value of $C$ can produce ten statistically equally likely solutions for $P$.

Here is an example  $5 = (P\ +\ K)\ mod\ 10$. For that equation to be solved $P$ and $K$ could be any of the following: 0 + 5 or 1 + 4 or 2 + 3 or 3 + 2 or 4 + 1 or 5 + 0 or 6 + 9 or 7 + 8 or 8 + 7 or 9 + 6. 

**Should I keep my checkerboard in secrecy?** 
<br> Knowing the checkerboard you use won't enable anyone to decrypt your ciphertext, but it could assist them in guessing the context of your message if they have pre-existing knowledge about the potential content of your communication.
## SOURCES:
This is a collection of excellent resources on the subject of One-Time Pad:
1. [THE COMPLETE GUIDE TO SECURE COMMUNICATIONS WITH THE ONE TIME PAD CIPHER (BY DIRK RIJMENANTS)](https://ciphermachinesandcryptology.com/papers/one_time_pad.pdf)
2. [Straddling Checkerboards (ciphermachinesandcryptology.com)](https://ciphermachinesandcryptology.com/en/table.htm)
3. [One-time-pad (ciphermachinesandcryptology.com)](https://ciphermachinesandcryptology.com/en/onetimepad.htm)
