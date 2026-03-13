# OverTheWire Bandit: Level 12
## Objective: Find the password stored in _data.txt_ 
*Impediments*: The person who is playing, hehe (just joking).  
From the instructions provided, we know that _data.txt_ is a **hexdump** file that has been repeatedly compressed.  

## Our tools ⚒️
The commands that will be "our weapons of war" are the following ones:  
`mkdir` <!--(or `mmktemp -d`)--> and `cp`: To work in a temporary folder (important for security and organization!).  
`xxd`: To convert the file to binary.  
`file`: To identify the file type.  
`gzip`, `bzip`, `tar`: The decompression trio.  
> Take note! `gzip` command is very picky about file extensions, so it's important to work with files that end in `.gz`.  

## Step-by-step instructions  
**Step 1: Creating a working space**  
Since we cannot create files in the home directory, we must move to `/tmp`. It's good practice to create a personal folder there.  
```bash
mkdir /tmp/workspace
```  
★☆ Pro tip: You can use `mktemp -d`. This command creates a unique temporary directory and returns its path. Now yo ucan use it!  
Then, copy *data.txt* to your personal folder and move there.  
```bash
cp data.txt /tmp/workspace
cd /tmp/workspace
```  

**Step 2: Reverting the Hexdump**  
The file _data.txt_ is a text representation of hexadecimal values. We use `xxd` with the `-r` (reverse) flag to turn it into a binary file.  
```bash
xxd -r data.txt > databin
```  

**Step 3: Peeling the onion 🧅 (Decompression layers)**  
Now the fun begins. We will use the `file` command to see what we are dealing with and then apply the correct tool.
★☆ Pro Tip: Instead of overwriting the same file, I redirected the output at each step (e.g., data1, data2, data3). This way, if I made a mistake, I didn't have to start from the very beginning.  
* **Layer 1: gzip**  
We find out the file type:
```bash
file databin
# Output: databin: gzip compressed data...
```
Since it's a **gzip**, we rename it so the tool recognizes it and decompress it:  
```bash
mv databin data1.gz
gzip -d data1.gz
# This creates a file named 'data1'
```
* **Layer 2: bzip2**  
Now we check the new file:
```bash
file data1
# Output: data1: bzip2 compressed data...
```
Time for the next tool!  
```bash
bzip -dc data1 > data2
# This will result in a file named 'data1.out' or similar
```
* **Layer 3: tar**  
```bash
file data3
# Output: data3 : POSIX tar archive...
```
We use the following command:  
```bash
tar -xf data3
```
But wait, **what happened?** Where is our decompressed file?
Don't worry! Use `ls -t`. This will show you the files ordered by modification time (most recent first). There you will find your extracted file.  
**Keep peeling!** Continue this process until `file` identifies the result as **ASCII text**.  
Finally, using `cat` on that last file will reveal the password for the next level!  

## My biggest stumbles
I ran into 2 problems...  
1. I didn't know about the `.gz` requirement at first, so I got stuck there. It's important to know how your tools work!  
2. `tar -xf` was really a headache. I tried to redirect `tar -xf` with `>`, but I only got empty files. I had to learn that `tar` extracts files directly to the disk instead of sending them to "standard output".  

## Conclusions: Final Thoughts & Lessons Learned  
**Organization is key:** Working in a temporary directory and naming each resulting file (data1, dat2, etc.) saved me. When I got lost in the middle, I just went back one step.  

**Trust `file`, not extensions:** In this level, I learned that a file's name doesn't matter; what matters is its actual content. Always verify with the `file` command before acting.  

**The Power of Redirection:** Using `>` to manage my outputs kept my workspace clean and my progress safe.  

On to Level 13! 🚀
