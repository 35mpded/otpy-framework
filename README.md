**This Python3 Framework is a digital adaptation of the One-Time Pad (OTP) Cipher. The framework provides access to functionality that you can use to build OTP cryptographic applications for secure & confidential communication.** This implementation of the One-Time Pad Cipher is identical to that used in  the number stations. Furthermore, since this framework follows the rules of the analogue implementation of the cipher, it facilitates a seamless transition from the digital version of the OTP cipher to the analogue version on pen and paper. You can try it out through the official console interface or the web application  [OTPy-Station](https://github.com/35mpded/otpy-station).

*This goes without saying but in order for the communication to remain secure, the device on which the OTPy Framework is used must not have any internet connectivity. You can use a standalone single-board computer (SBC) like the Raspberry Pi or something similar for this purpose. Furthermore, using the cipher on internet leaves a digital footprint, which means that while the cipher itself may be unbreakable, the encrypted messages could still be traced back to you.*

# DOCUMENTATION
Note that If the One-Time Pad Cipher is new to you, the upcoming sections might be a bit confusing. If that is the case, you can familiarize yourself with the cipher by reading the [The One-Time Pad Cipher](https://github.com/35mpded/otpy-framework/blob/main/docs/the_otp_cipher.md) document or by watching the [The ULTIMATE One Time Pad Tutorial](https://www.youtube.com/watch?v=rCeWtQERizA&lc=UgxNO0gFvF-qLf0YiYF4AaABAg.9y0-NsbHT239yEB73sMyWK).
## FEATURES
The OTPy Framework is composed of different components which all have a specific role within this digital implementation of the One-Time Pad Cipher. Each of the components can be used independently from each other.  In the sections the follow, detailed code examples will be provided to showcase the usage of various components. However, here is an overview of the basic features:
- Generate One-Time Pad  keys by using a cryptographically secure random number generator (CSRNG).
- Save One-Time Pad keys to JSONL files.
- Encode and decode messages through the use of a checkerboard.
- Easily create & use custom checkerboards from CSV files for encoding and decoding tasks.
- Encrypt and decrypt messages though modulus 10.
- Automatically identify suitable One-Time Pad keys for encryption and decryption.
- Automatically destroy One-Time Pad keys once they're used.
- Synthesize ciphertext to speech, enabling transmission over audio mediums such as radio (number stations), YouTube, and whatever.
- Modularity - you can independently synthesize, encrypt/decrypt or encode/decode messages.

## otpyf.csv_checkerboard_to_dict()
This component of the OTPy Framework allows you to dynamically create python dictionary with mappings obtained from any CSV checkerboard that adheres to the format of the examples below. 
|   | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8   | 9   |
|---|---|---|---|---|---|---|---|---|-----|-----|
|   | A | T |   | O | N | E |   | S | I   | R   |
| 2 | B | C | D | F | G | H | J | K | L   | M   |
| 6 | P | Q | U | V | W | X | Y | Z | F/L | /   |

The straddle, unstraddle, and the csv checkerboard to dictionary algorithms support checkerboards of various sizes, from smaller ones like the "AT ONE SIR" to such larger than "XRAY".  You can create even larger checkerboards than "XRAY" by adding additional digits to the row designators (leftmost digits). *Just note that I have not tested it extensively with two-digit row designators, so be careful of dragons when using such.*

|   | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8   | 9   |
|---|---|---|---|---|---|---|---|---|-----|-----|
|   | X | R | A | Y |   |   |   |   |     |     |
| 4 | B | C | D | E | F | G | H | I | J   | K   |
| 5 | L | M | N | O | P | Q | S | T | U   | V   |
| 6 | W | Z | ~ | ` | ! | @ | # | $ | %   | ^   |
| 7 | & | * | ( | ) | _ | - | + | = | [   | ]   |
| 8 | { | } | \ | \| | ; | : | ' | " | ,   | <   |
| 9 | . | > | / | ? | ° | ♥ | ■ |   | F/L | SPC |

**Important things to remember:**
- 'SPC' and 'F/L' are reserved designations for special codes, yet their inclusion in the checkerboard is only optional.
	- 'SPC' is used to indicate whitespace (0x20).
	- 'F/L' is used to indicate letter/figure switch (A-Z/0-9).
- Empty fields will not break your checkerboard, as long as they're within the grid boundaries.
- Theoretically you can use any character supported by your environment, such as special symbols, Cyrillic and so on. 

*Note that for the time being you can only change the 'F/L' designation. Future updates will allow to change the 'SPC' as well.* 

### USAGE
The `csv_checkerboard_to_dict()` requires the following arguments:
-	The absolute path and filename of the .CSV checkerboard file


Example usage of the `csv_checkerboard_to_dict()` component:

1. Create the below checkerboard in a CSV file `xray.csv`:<br>*You can change the name if you want to, as long as you reflect it in the below script*
	```
	,0,1,2,3,4,5,6,7,8,9
	,X,R,A,Y,,,,,,
	4,B,C,D,E,F,G,H,I,J,K
	5,L,M,N,O,P,Q,S,T,U,V
	6,W,Z,~,`,!,@,#,$,%,^
	7,&,*,(,),_,-,+,=,[,]
	8,{,},\,|,;,:,',"""",",",<
	9,.,>,/,?,°,♥,■,,F/L,SPC
	```

4. Create  the script:
	```
	# Import the otpy framework
	import otpyf
	
	# Pass the checkerboard csv to the `csv_checkerboard_to_dict` and store the returned dictionary in a variable `checkerboard_dict`
	checkerboard_dict= otpyf.csv_checkerboard_to_dict("./checkerboards/xray.csv")
	
	#  Print the dictionary
	print(checkerboard_dict)
	```
## otpyf.straddle()
The encoder algorithm needs to be supplied with a checkerboard dictionary (mapping) and a text string in order to carry out the encoding operation. Once supplied with this data, the algorithm will:
-	Transform (i.e. encode) any characters into their respective checkerboard numbers.
- Detect any digits in the plaintext and enclose them with the 'F/L' switch code. E.g. '123' is transformed into '**98**123**98**'. *This approach conserves space on the checkerboard by eliminating the need to individually map digits (0-9). The other reason is to ensure backward compatibility, as in the old days (understand pen and paper) this was the historical practice.*
-	Handle any other special purpose codes like 'SPC'.
-	Verifies if the plaincode meets a specific length requirement and applies padding as necessary to reach the length requirement.
-	Returns the plaincode (i.e. the encoded text).

### USAGE
The `straddle()` requires the following arguments:
-	A text string for encoding
-	Fullpath & name of the checkerboard dictionary used by the encoder
-	The designator for switching between numbers and letters, as defined in the checkerboard.
-	The size to which the plaincode will be padded, in case it is shorter than the specified length.

Example usage of the `straddle()` component:
```
# Import the otpy framework
import otpyf

# Create a mapping for the specified checkerboard
checkerboard_dict = otpyf.csv_checkerboard_to_dict("./checkerboards/xray.csv")

# Define the message you want to encode
plaintext = "Hello World"

# Encode the plaintext by using the straddle component
plaincode = otpyf.straddle(plaintext, checkerboard_dict, checkerboard_dict['F/L'], 30)

# Print the plaincode to terminal
print(plaincode)
```
Note that we specified the switch code for transitioning between A-Z and 0-9 as 'F/L'. This can be altered, but any changes must be reflected accordingly in the checkerboard CSV file.

## otpyf.unstraddle()
Similar to the encoder, the decoder algorithm requires a checkerboard dictionary (mapping) as well. However, in this case, it needs a numeric string (plaincode) instead of text. Once supplied with this data, the algorithm will:
- Create a reverse mapping (digits to letters) based on the checkerboard dictionary mapping.
- Decode digits into their respective checkerboard characters. This operation differs slightly from the encoding process as it iterates over the given code lengths from longest to shortest. For example, consider the encoded string '12345'. Both '123' and '45' are valid codes in the checkerboard, but '12345' is also a valid code. By examining the defined code length, the decoder accurately interprets it as '12345' instead of mistakenly interpreting it as '123' followed by '45'.
- Detect any digits in the plaincode that are enclosed within the 'F/L' switch for decoding as numbers. E.g. '**98**123**98**' would be decoded as '123'.
- Handle any other special purpose codes like 'SPC'.
- Returns the decoded plaintext


### USAGE
The `unstraddle()` requires the following arguments:
-	A number string for decoding
-	Fullpath & name of the checkerboard dictionary used by the decoder
-	The designator for switching between numbers and letters, as defined in the checkerboard.

Example usage of the `unstraddle()` component:
```
# Import the otpy framework
import otpyf

#  Create a mapping for the checkerboard
checkerboard_dict = otpyf.csv_checkerboard_to_dict("./checkerboards/xray.csv")

# Define the plaincode you want to decode
plaincode = "464350505399605315042076777701"

#  Decode the plaincode to plaintext by using the unstraddle component
plaintext = otpyf.unstraddle(plaincode, checkerboard_dict, checkerboard_dict['F/L'])

# Print the plaintext to terminal
print(plaintext)
```
Note that we specified the switch code for transitioning between A-Z and 0-9 as 'F/L'. This can be altered, but any changes must be reflected accordingly in the checkerboard CSV file.

##  otpy.generate_csrng_numbers()
The OTPy Framework has a CSRNG function that implements the python `secrets` module to create One-Time Pad keys. This module generates random numbers by utilizing noise from the user's computer activity and hardware components. That is the closest you'll get to truly random numbers (and yes, they are secure enough for use). 

### USAGE
The `generate_csrng_numbers()` requires the following arguments:
- Number of pads it will generate
- The length of each key

Example usage of the `generate_csrng_numbers()` component:
```
# Import the otpy framework
import otpyf

# Define the ammount of pads
num_pads = 350

# Define the lenght of each pad key
key_length = 35

# Pass the above data to the CSRNG component and store the generated numbers in a variable.
crng_numbers = otpyf.generate_csrng_numbers(key_length, num_pads)

# Handle the returned numbers however you want. For example, you can print them to terminal.
print(crng_numbers)
```
# otpyf.save_to_jsonline_file
The OTPy Framework can save generated pads from the CSRNG component into a JSONL file. Every line in the JSONL file corresponds to a One-Time Pad (sheet/pad/key...however you want to call it), as shown below:
```json
"70276593385401807460878..."
"38238593242466872775364..."
"85401807460878593218036..."
```
To ensure secure communication, it's necessary to create two sets of JSONL files and exchange them physically between parties, such as via flash drives. This is because using digital methods to transfer OTP keys is not secure. 
```
# Party A
B_pads_send.json
A_pads_recv.json

# Party B
A_pads_send.json
B_pads_recv.json
```

The reasoning behind this is rooted in secure communication principles, especially relevant in One-Time Pad (OTP) cryptography.  By having separate files like `B_pads_send.json` for Party A and `A_pads_send.json` for Party B, each party uses a different key for sending messages. This approach reduces the risk of key reuse and interception. If the same key were used for both sending and receiving, it would increase the chance of compromising the cipher. Similiary, if the keys are intercepted or exposed, the security of your cipher is equally jeopardized. *This framework is a digital implementation of the traditional OTP system, which is known for its theoretical unbreakability when used correctly. By adhering to the core principles of security, which include separate, unique, and securely exchanged keys the OTPy Framework can provide a high level of security in digital communications, but following these core principles is your responsibility, the framework will not force you to.*


### USAGE
The `save_to_jsonline_file()` requires the following arguments:
- The JSON file where the One-Time Pad keys will be stored.
- The One-Time Pad keys produced by the CSRNG component

Example usage of the `save_to_jsonline_file()` component:
```
# Import the otpy framework
import otpyf

# Define the ammount of pads
num_pads = 350

# Define the lenght of each pad key
key_length = 35

# Use two instances of the `otpyf.generate_csrng_numbers` to generate two different sets of number pads.
crng_numbers_A = otpyf.generate_csrng_numbers(key_length, num_pads)
crng_numbers_B = otpyf.generate_csrng_numbers(key_length, num_pads)

# Create a try block. This is to catch any errors of the `save_to_jsonline_file` component.
try:
   # Save the number pads, according to their pair, by using the `otpy.save_to_jsonline_file` component
   otpyf.save_to_jsonline_file("./A_pads_send.json", crng_numbers_A)
   otpyf.save_to_jsonline_file("./B_pads_recv.json", crng_numbers_A)
   otpyf.save_to_jsonline_file("./B_pads_send.json", crng_numbers_B)
   otpyf.save_to_jsonline_file("./A_pads_recv.json", crng_numbers_B)

except FileExistsError as e:
    print(e)
```


## otpyf.mod10_encrypt/mod10_decrypt()
Currently, the framework is configured to utilize a specific variation of OTP encryption/decryption scheme, $P\ −\ K\ →\ C$ for encrypt operation and  $C\ +\ K\ →\ P$ for decrypt operation.  For different encryption schemes please see the  [The One-Time Pad Cipher](https://github.com/35mpded/otpy-framework/blob/main/docs/the_otp_cipher.md) document. You should note that altering this would require modifications to the `mod10_encrypt` and `mod10_decrypt` functions.  *However, this should not be a concern unless you intend to perform encryption and decryption by hand.*


The One-Time Pad Python3 Framework has two ways of encrypting/decrypting. You can either provide the OTP key as a string or the framework can automatically identify it from JSONL files. In this section we'll review the manual method.

### USAGE
The `mod10_encrypt()` requires the following arguments:
- A numeric string (plaincode) for encryption.
- An OTP key in string format, used to encrypt the plaincode.
- An integer value used as a safety check to ensure that the plaintext and key match a predetermined length.


Example usage of the `mod10_encrypt()` component:
```
# Import the otpy framework
import otpyf

# Define the length safety check
safety_check = 30

# Store the plaincode which will be encrypted
plaincode = "464350505399605315042076777701"

# Store the encryption key
otp_enc_key = "898528718447134862532089505436"

# Pass the plaincode, otp enc key, and safety check value to the `mod10_encrypt` component.
# Store the encrypted result into a variable
ciphertext = otpyf .mod10_encrypt(plaincode, otp_enc_key, safety_check)

# Print the encrypted result (ciphertext)
print(ciphertext)
```

The `mod10_decrypt()` requires the following arguments:
- A numeric string (ciphertext) for decryption
- An OTP key in string format, used to decrypt the ciphertext.


Example usage of the `mod10_decrypt()` component:
```
# Import the otpy framework
import otpyf

# Store the ciphertext which will be decrypted
ciphertext = "676832897952571553510097272375"

# Store the decryption key
otp_dec_key = "898528718447134862532089505436"

# Pass the ciphertext & otp dec key to the `mod10_decrypt` component.
# Store the decrypted result into a variable
plaincode = otpyf.mod10_decrypt(ciphertext, otp_dec_key)

# Print the decrypted result (plaincode)
print(plaincode)
```
Notice that the safety check (length) is not required? This is deliberate, as the only consequence might be an inability to decrypt the cipher, without jeopardizing its security.

## otpyf.encrypt_init/decript_init()
The `encrypt_init()` and `decrypt_init()`  utilize the `mod10_encrypt()` and `mod10_decrypt()` functions. However, instead of requiring you to specify a key, they are designed to dynamically locate the OTP key from JSONL files.


The `encrypt_init()` function is designed to carry out the following actions:
- Select a random key is from a given JSONL file.
- Extract the first 5 digits as an ID for OTP key and the remaining digits as the OTP key itself.
- Pass the OTP key without the ID (i.e the first 5 digits) and the plaincode to the `mod_encrypt()` functions.<br>
*Note that the first 5 digits won't be used in the encryption process.*
- Encrypt the provided plaincode and append the OTP key ID to the ciphertext
- Return the ciphertext, as well as the used OTP key and its ID.

The `decrypt_init()` function is designed to carry out the following actions:
- Extract the first 5 digits of the ciphertext as an ID for the OTP key, while the remaining digits will be extracted as the ciphertext.
- The OTP key ID is utilized to recursively search through JSON files, in a given directory, in order to identify the OTP decryption key. 
*This is done for your convenience in scenarios where you are managing multiple decryption pads.* 
- Using the identified OTP decryption key, the ciphertext will be decrypted to plaincode.<br>
*Note that the first 5 digits won't be used in the decryption process as it will yield nonsensical results. This is because they are not used during the encryption process.*


### USAGE
The `encrypt_init()` requires the following arguments:
- A json file from which it will get an OTP key, used for encrypting the plaincode.
- A numeric string (plaincode) that it will encrypt.
- A length used for the safety check of the OTP key and plaincode.

Example usage of the `encrypt_init()` component:
```
# Import the otpy framework
import otpyf

# define the length used for the safety check of the OTP key and plaincode.
safety_check = 30

# Store the plaincode for encryption
plaincode = "464350505399605315042076777701"

# Encrypt the plaincode with random OTP key by using the encrypt_init component.
# Arguments: <json file with keys>, <plaincode>, <plaincode & key length>
# After the encryption is done the component will return 
# the ciphertext, OTP key used for encryption and it's ID (first 5 digits)
ciphertext, otp_key, otp_key_id = otpyf.encrypt_init("A_pads_send.json", plaincode, safety_check)

# Print to terminal the ciphertext
print(f"Ciphertext:\n{ciphertext}")

# Print to terminal the OTP key used for encryption
print(f"\nOTP Key:\n{otp_key}")

# Print to terminal the ID of the OTP key used for encryption
print(f"\nOTP Key ID:\n{otp_key_id}")
```
The `decrypt_init()` requires the following arguments:
- A directory with JSON files containing OTP keys.
- A numeric string (ciphertext) that it will decrypt.

Example usage of the `decrypt_init()` component:
```
# Import the otpy framework
import otpyf

# Store the ciphertext which will be decrypted
# Make sure it's the same as the one you've retrieved 
# form the encrypt_init example
ciphertext = "90874195970708486642187389261120878"

# Decrypt the ciphertext by automatic key identification using the decrypt_init component.
# Arguments: <json directory>, <ciphertext>
# After the decryption is done the component will return 
# the plaincode, OTP key used for decryption and it's ID (first 5 digits)
plaincode, otp_key, otp_key_id = otpyf.decrypt_init("./pads/", ciphertext)

# Print to terminal the plaincode to terminal
print(f"Plaincode:\n{plaincode}")

# Print to terminal the OTP key used for decryption
print(f"\nOTP Key:\n{otp_key}")

# Print to terminal the ID of the OTP key used for decryption
print(f"\nOTP Key ID:\n{otp_key_id}")
```

## otpyf.AudioMessageSynthesizer()
The audio synthesizer component is designed to transforms ciphertext into a voice message, enabling transmission over audio mediums such as radio (number stations), YouTube, and whatever else. The `AudioMessageSynthesizer` class utilizes the core Python module `wave` to transform any alphanumeric message (A-Z/0-9) into audio by using a predefined list of .wav sounds, each corresponding to a specific alphanumeric character.

**AudioMessageSynthesizer Features**
- Convert alphanumeric characters to their corresponding sounds.
- Convert symbols such as the period character (.), newlines (\n), and others into predefined .WAV audio files.  This might be useful to indicate different stuff inside your audio message.
*More will be added in the future releases.*
- Include custom .WAV sounds at the beginning or the end of the synthesized message. Could be useful to indicate different callsigns and so on.

-   Transform alphanumeric characters into designated sound files.
-   Transform special symbols like punctuation marks (.), newline symbol (\n), and similar characters into custom .WAV sounds. This feature can help differentiate various elements in your audio messages. _Future updates will introduce additional features._
-   Add custom .WAV sounds at the start or end of the synthesized message, which can be effective for signaling different callsigns or other things, like keeping a radio frequency open for a given interval.

**General settings:**

Currently, these are the settings that you can adjust:
- Specify custom names for the phonetic & numeric .WAV sound files.

- Specify the custom .WAV sound files which are to be included at the start and finish of the synthesized message.


At present, these settings cannot be altered. However, in the future releases you'll be able to change them:
- There is a time gap of 50ms between spoken characters.

-   There is a time gap of 400ms between whitespace and comma (`,`) symbols, enabling the segmentation of the ciphertext into groups, as is done in number stations.

- Custom .WAV files will be utilized for predefined symbols like the period (`.`). 
*At present, the default is `_blank.wav`.* 

- The synthesized ciphertext will be outputted in a file named `ciphertext.wav`. 
- The fullpath and directory name used to store the .WAV files.

**Audio settings:**

Custom audio files should be mono, resampled to 22050Hz, exported as .WAV - 16-bit PCM

**Directory structure:**

The AudioMessageSynthesizer currently requires .WAV files to be located in a ./vo/ directory, following a specific structure, relative to the path from which it is run.


```
├── vo
│   ├── _blank.wav
│   ├── _end.wav
│   ├── one.wav
│   ├── two.wav
│   ├── three.wav
│   ├── four.wav
│   ├── five.wav
│   ├── six.wav
│   ├── seven.wav
│   ├── eight.wav
│   ├── nine.wav
│   ├── zero.wav
│   ├── phonetic
│   │   ├── alpha.wav
│   │   ├── bravo.wav
│   │   ├── charlie.wav
│   │   ├── delta.wav
│   │   ├── echo.wav
│   │   ├── foxtrot.wav
│   │   ├── golf.wav
│   │   ├── hotel.wav
│   │   ├── india.wav
│   │   ├── juliet.wav
│   │   ├── kilo.wav
│   │   ├── lima.wav
│   │   ├── mike.wav
│   │   ├── november.wav
│   │   ├── oscar.wav
│   │   ├── papa.wav
│   │   ├── quebec.wav
│   │   ├── romeo.wav
│   │   ├── sierra.wav
│   │   ├── tango.wav
│   │   ├── uniform.wav
│   │   ├── victor.wav
│   │   ├── whiskey.wav
│   │   ├── x-ray.wav
│   │   ├── yankee.wav
│   │   └── zulu.wav
│   ├── misc
│   │   └── buzzer.wav
```


### USAGE
The `AudioMessageSynthesizer()` requires the following arguments:
-	The message it will synthesize.
-	The fullpath & name of the .wav sound file it will include at the start of the synthesized message.
-	The fullpath & name of the .wav sound file it will include at the end of the synthesized message.
-	The name of the .wav sound files it will use for synthesizing numbers from 0-9.<br>
*Keep in mind that the specific names are irrelevant. The first file in the sequence is used for num 0, and the last file is used for num 9.*
-	The name of the .wav sound files it will use for synthesizing letters from A-Z.<br>
   *Keep in mind that the specific names are irrelevant. They are automatically assigned based on the alphabetical position of the character - the first sound file corresponds to A, and the final one in the sequence corresponds to Z.*

Example usage of the `AudioMessageSynthesizer()` component:<br>
*Note that you'll need to download the voice data from the [vocoder repostory](https://github.com/35mpded/otpy-vocoder-data).*

```
# Import the otpy framework
import otpyf

# Define the message which will be synthesized
ciphertext = "124"

# Define the phonetic and numeric sound files.
numbers = ["zero.wav", "one.wav", "two.wav", "three.wav", "four.wav", "five.wav", "six.wav", "seven.wav", "eight.wav", "nine.wav"]
alpha = ["alpha", "bravo", "charlie", "delta", "echo", "foxtrot", "golf", "hotel", "india", "juliet", "kilo", "lima", "mike", "november", "oscar", "papa", "quebec", "romeo", "sierra", "tango", "uniform", "victor", "whiskey", "x-ray", "yankee", "zulu"]

# Define the sound files which will be included before and after the message. 
# You can include the same file multiple times or use combination of different sound files.
prepend_audio = "./vo/misc/buzzer.wav,vo/misc/buzzer.wav"
append_audio = "./vo/_end.wav"

# Synthesize the message
synthesizer = otpyf.AudioMessageSynthesizer(ciphertext, prepend_audio, append_audio, numbers, alpha)
synthesizer.construct_wav()
```

## Other functions
This section provides an overview of several miscellaneous features within the OPTy Framework. While these features are not directly related to the OTP cipher, they are nonetheless quite useful.

### otpyf.print_checkerboard_table()
This function displays the checkerboard data from the .CSV file as a neatly formatted table in the terminal. It is particularly useful for applications that run in the console.

Required arguments:
- The fullpath and name of the checkerboard .CSV file.
```
# Import the otpy framework
import otpyf

#  Print the checkerboard into a text table
otpyf.print_checkerboard_table("./checkerboards/xray.csv")
```

### otpyf.split_into_groups_of_five()
This function divides a given numeric string into groups of five digits each. Number Stations use this method to break cipher messages into groups of five for easier handling. When working with the cipher on pen and paper, it's also separated into five-digit groups to enhance readability.

Required arguments:
- A text or numeric string that will be split.
```
# Import the otpy framework
import otpyf

# Store the cipher that'll be split
cipher = "05927406712385651831"

# Split & print the cipher
print(otpyf.split_into_groups_of_five(cipher))
```

### otpyf.clear()
This function clears and resets the terminal display, providing the user with a fresh, uncluttered screen. Ideal for organizing menus in terminal-based applications.

Required arguments:
-	Doesn't require any.

**Menu example**
```
# Import the otpy framework
import otpyf

def print_menu():
    # Clear the terminal screen
    otpyf.clear()
    print ("[*] This is an example menu\n")
    print ("[1] Do something\n[2] Do something")
loop=True

while loop:
    # Print the menu while the loop is active
    print_menu()
    choice = input("\nSelect an action [1-2]: ")

    if choice == "1":
        # Clear the terminal screen
        otpyf.clear()
        input("\n[*] Option 1 selected.\nPress enter key to continue...")

    elif choice=="2":
        # Clear the terminal screen
        otpyf.clear()
        input("\n[*] Option 2 selected.\nPress enter key to continue...")

    else:
       input("\nThis action does not exist. Press enter key to continue...")
```
