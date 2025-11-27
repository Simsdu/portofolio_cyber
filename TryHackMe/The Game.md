

# The Game

##### Category: Reverse Engineering

##### Difficulty: Easy

##### Event: Hackfinity Battle 2025 CTF Event

##### Solver: Simon

---

1. **Introduction**
   
   Cipher has gone dark, but intel reveals he’s hiding critical secrets 
   inside Tetris, a popular video game. Hack it and uncover the encrypted 
   data buried in its code.

2. **File Extraction**
   
   I created a directory, moved the archive inside it, and extracted its contents.
   
   ```bash
   cd Documents/
   mkdir CTF-game
   cd CTF-game
   
   cp /home/simsdu/Downloads/Tetrix.exe-1741979048280.zip
   
   
   unzip Tetrix.exe-1741979048280.zip
   ```
   
   The extracted files are:
   
   ```bash
   Tetrix.exe
   __MACOSX/
   ```

3. **Static Analysis of the .exe**
   
   For this challenge, a quick and efficient thing is to use `strings` to search for human-readable text inside the binary.
   Because TryHackMe CTF often follow patterns `thm{`, i performed a targeted search.
   
   ```bash
   strings -n 10 Tetrix.exe | grep -i "thm{"
   ```
   
   `grep -i` for case insensitive search.
   
   *Result:*
   
   ```bash
   THM{I_CAN_READ_IT_ALL}
   ```
   
   The flag appears directly in plaintext inside the binary.

4. **Conclusion**
   
   A simple introduction to reverse-engineering challenge, No debugging, disassembly or advanced tooling required, `strings` alone is enough to get the flag.
