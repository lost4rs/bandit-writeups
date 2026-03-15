# OverTheWire Bandit: Level 12
## Objective: Find the password stored in _data.txt_  
From the instructions provided, we know that _data.txt_ is a **hexdump** file that has been repeatedly compressed.  

## Tools ⚒️
`mkdir` <!--(or `mmktemp -d`)--> and `cp`: To work in a temporary folder (important for security and organization!).  
`xxd`: To convert the file to binary.  
`file`: To identify the file type.  
`zcat`, `bzcat`, `tar`: The decompression trio.  
> The `zcat` command is very picky about file extensions, so it's important to work with files that end in `.gz`.  

## Methodology & Execution 💡
**Creating a working space**  
Since we cannot create files in the home directory, I moved the target file to a temporary directory (which has a "unique" name):  
```bash
mkdir /tmp/workspace-bandit12
```  
<!-- ★☆ Pro tip: You can use `mktemp -d`. This command creates a unique temporary directory and returns its path, then you can use it for your workspace. -->
Then, copy *data.txt* to your personal folder and move there.  
```bash
cp data.txt /tmp/workspace-bandit12
cd /tmp/workspace-bandit12
```  

**Reverting the Hexdump**  
I used `xxd` with the `-r` (reverse) flag to turn it into a binary file.  
```bash
xxd -r data.txt > databin
```  

**Peeling the onion 🧅 (Decompression layers)**  
I used the `file` command to see what I was dealing with and then apply the correct tool.  
★☆ Pro Tip: Instead of overwriting the same file, I redirected the output at each step (e.g., data1, data2, data3). This way, if I made a mistake, I didn't have to start from the very beginning.  
* **Layer 1:** Identified as *gzip* compressed data.  
After identifying the file type as **gzip**, we rename it so the tool recognizes it and decompress it:  
`mv databin data1.gz && zcat data1.gz > data2`  
* **Layer 2:** Identified as *bzip2* compressed data.   
`bzcat data2 > data3`  
* **Layer 3:** Identified as *gzip* compressed data.  
`mv data3 data3.gz && zcat data3.gz > data4`  
* **Layer 4:** Identified as POSIX *tar* archive.  
`tar -xf data4`  
Obs: Since we cannot redirect the decompressed file with `tar -xf`, I used `ls -t` to find the extracted file.
* **Layer 5:** Identified as POSIX *tar* archive.  
`tar -xf data5.bin`  
* **Layer 6:** Identified as *bzip2* compressed data.   
`bzcat data6.bin > data7`  
* **Layer 7:** Identified as POSIX *tar* archive.  
`tar -xf data7`  
* **Layer 8:** Identified as *gzip* compressed data.  
`mv data8.bin data8.gz && zcat data8.gz > data9`
  
The final extraction revealed an **ASCII text** file containing the password for Level13.  
Finally, using `cat` on that last file revealed the password!  

## My biggest challenges     
I ran into 2 problems...  
1. I didn't know about the `.gz` requirement at first, so I got stuck there. Then I understood the functionality of this command and applied it correctly.  
2. `tar -xf` was really a headache. I tried to redirect `tar -xf` with `>`, but I only got empty files because `tar` extracts files directly to the disk instead of sending them to "standard output".  

## Conclusions: Lessons learned ✨ 
* Don't trust extensions: In this level, files don't have names like .zip or .tar. I learned to rely 100% on the `file` command. It’s like looking at the DNA of the file instead of just the label.

* Organization is a lifesaver: I learned that keeping a clean workspace in /tmp/ and naming things clearly saves a lot of headaches.  

* The "Scripting" Hint: Doing this manually was a bit tedious. It made me realize that in a real scenario with 100 layers, I’d definitely need to write a small script to automate the process. It's a great motivator to keep improving my Bash skills.  
  
**On to Level 13!** 🚀  

