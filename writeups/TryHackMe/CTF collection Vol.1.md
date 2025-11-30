# CTF collection Vol.1

##### Difficulty: Easy

##### Solver: Simon

##### Maker: DesKel

---

##### Introduction:

This is a collection of easy CTF for sharpenning up CTF skills

1. **What does the base said?**
   
   Can you decode the following?
   
   ``` bash
   - VEhNe2p1NTdfZDNjMGQzXzdoM19iNDUzfQ==
   ```
   
   We get the flag by using CyberChef to decode it, it's encoded in `Base64`
   
   ![](/home/simsdu/.config/marktext/images/2025-11-21-17-01-27-image.png)

2. **Meta meta**
   
   To solve this one, we have to check the meta data of the image, using a linux tool :`exif` we can extract these data and get the flag.
   
   ```bash
   --------------------+---------------------
   Tag                 |Value
   --------------------+---------------------
   Resolution Unit     |Inch
   YCbCr Positioning   |Centered
   X-Resolution        |72
   Y-Resolution        |72
   Exif Version        |Exif Version 2.31
   Components Configura|Y Cb Cr -
   FlashPixVersion     |FlashPix Version 1.0
   Camera Owner Name   |THM{Flag is here}
   Color Space         |Uncalibrated
   --------------------+----------------------
   ```

3. **Mon, are we going to be okay?**
   
   Here, the flag is hidden inside the image, this is steganography, to get it, i use a Linux tool: `steghide`
   
   ```bash
   steghide extract -sf '/Extinction.jpg' 
   Enter passphrase: 
   wrote extracted data to "Final_message.txt".
   
   
   cat Final_message.txt
   THM{flag is here}
   ```

4. **Erm......Magick**
   
   Here the flag is just same color as background, by just inpecting we can get it.
   
   ![](/home/simsdu/.config/marktext/images/2025-11-21-17-09-58-image.png)

5. **QR**
   
   Here, the image is a QR code, just by using a simple QR code scanner online we get the flag.
   
   ![](/home/simsdu/.config/marktext/images/2025-11-21-17-11-50-image.png)

6. **Reverse it or read it?**
   
   Only using the `cat` command, we can get the flag.
   
   ```bash
   /Downloads/hello_1577977122465.hello
   H��THM{flag is there}Hello there,
   ```

7. **Another decoding stuff**
   
   Can you decode it?
   
   ```bash
   1. 3agrSy1CewF9v8ukcSkPSYm3oKUoByUpKG4L
   ```
   
   We get the flag by using CyberChef to decode it, it's encoded in `Base58`.
   
   ![](/home/simsdu/.config/marktext/images/2025-11-21-17-18-37-image.png)

8. **Left or right**
   
   Left, right, left, right... Rot 13 is too mainstream. Solve this
   
   ```bash
   1. MAF{atbe_max_vtxltk}
   ```
   
   This one is encoded in ROT7, using an online tool we get the flag.
   
   ![](/home/simsdu/.config/marktext/images/2025-11-21-17-20-55-image.png)

9. **Make a comment**
   
   Here, the flag is hidden in the html comment when we inspect it.
   
   ![](/home/simsdu/.config/marktext/images/2025-11-21-17-23-38-image.png)

10. **Can you fix it?**
    
    Here, the PNG file is messed up, we have to use an online Hex editing tool. And we can see that the file signature isnt the one of a PNG one, so we just have to modify it to be: ``89 50 4E 47 0D 0A 1A 0A``, then save the file and when we open the image, we get the flag.
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-27-38-image.png)

11. **Read it**
    
    To get this flag, we have to dig on Tryhackme social account, for this we are going to use Google dorks : `inurl:"social" & intitle:"tryhackme" & intext:"thm"`. And brute force it trying different social network: twitter, facebook, instagram, reddit. The flag is inside a reddit post that we found with the Google dorks: `inurl:"reddit.com" & intitle:"tryhackme" & intext:"thm"`.
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-31-52-image.png)

12. **Spin my head**
    
    Decode this:
    
    `++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>++++++++++++++.------------.+++++.>+++++++++++++++++++++++.<<++++++++++++++++++.>>-------------------.---------.++++++++++++++.++++++++++++.<++++++++++++++++++.+++++++++.<+++.+.>----.>++++.`
    This is a language called "Brainfuck", using an online decoder we get the flag.
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-34-05-image.png)

13. **An exclusive!**
    
    Exclusive strings for everyone!
    
    S1: 44585d6b2368737c65252166234f20626d  
    S2: 1010101010101010101010101010101010
    
    For this one, we are going to use an online XOR calculator, we have an output in base 16, then decode it with an online base16 decoder and get the flag
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-37-48-image.png)

14. **Binary walk**
    
    For this one, we are going to use a Linux tool: `Binwalk`, useful for extracting and analyzing content from a binary file.
    
    ```bash
    binwalk 'Downloads/hell_1578018688127.jpg' 
    
    DECIMAL       HEXADECIMAL     DESCRIPTION
    --------------------------------------------------------------------------------
    0             0x0             JPEG image data, JFIF standard 1.02
    30            0x1E            TIFF image data, big-endian, offset of first image directory: 8
    265845        0x40E75         Zip archive data, at least v2.0 to extract, uncompressed size: 69, name: hello_there.txt
    266099        0x40F73         End of Zip archive, footer length: 22
    ```
    
    We have to unzip into the `hello_there.txt` file
    
    ```bash
    cat hello_there.txt 
    Thank you for extracting me, you are the best!
    
    THM{flag is here}
    ```

15. **Darkness**
    
    We are using `Stegsolve` for this one, 
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-46-44-image.png)

16. **A sounding QR**
    
    We use the same QR code scanner as step 5, it gives us a soundcloud link, the voice inside it give the flag.

17. **Dig up the past**
    
    Targetted website: https://www.embeddedhacker.com/[](http://www.embeddedhacker.com)  
    Targetted time: 2 January 2020
    
    We are using The Wayback Machine, it's a website that take snapshots of websites in past. By getting at the given url to the given date, we ccan get the flag.
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-50-59-image.png)

18. **Uncrackable!**
    
    Can you solve the following? By the way, I lost the key. Sorry >.<  
    
    ```bash
    MYKAHODTQ{RVG_YVGGK_FAL_WXF}
    
    Flag format: TRYHACKME{FLAG IN ALL CAP}
    ```
    
    We have to guess the key of vigenere cipher, using cyberchef, we guess it's `THM`.
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-53-33-image.png)

19. **Small bases**
    
    Decode the following text.
    
    ```bash
    581695969015253365094191591547859387620042736036246486373595515576333693
    ```
    
    The hint explain us to decode decimal to hexadecimal, then to ascii.
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-56-14-image.png)
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-56-44-image.png)

20. **Read the packet**
    
    I just hacked my neighbor's WiFi and try to capture some packet. He must be up to no good. Help me find it.
    
    We have to read the packet with wireshark, search for the flag one, put it into stream and we get the flag.
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-58-48-image.png)
    
    ![](/home/simsdu/.config/marktext/images/2025-11-21-17-59-22-image.png)



# 
