---
title: "Writing Scripts and Working with Data"
teaching: 20
exercises: 20
questions:
- for loops are used for iteration
- How can we automate a commonly used set of commands?
objectives:
- Use the `nano` text editor to modify text files.
- Write a basic shell script.
- Use `chmod` to make a script an executable program.
keypoints:
- Scripts are a collection of commands executed together.
- Scripts are executable text files.
- Nano is a text editor.
---

<script language="javascript" type="text/javascript">
function set_page_view_defaults() {
    document.getElementById('div_win').style.display = 'block';
    document.getElementById('div_unix').style.display = 'none';
};

function change_content_by_platform(form_control){
    if (!form_control || document.getElementById(form_control).value == 'win') {
        set_page_view_defaults();
    } else if (document.getElementById(form_control).value == 'unix') {
        document.getElementById('div_win').style.display = 'none';
        document.getElementById('div_unix').style.display = 'block';
    } else {
        alert("Error: Missing platform value for 'change_content_by_platform()' script!");
    }
}

window.onload = set_page_view_defaults;
</script>

## Writing for loops

Loops are key to productivity improvements through automation as they allow us to execute commands repeatedly. 
Similar to wildcards and tab completion, using loops also reduces the amount of typing (and typing mistakes). 
Loops are helpful when performing operations on groups of sequencing files, such as unzipping or trimming multiple
files. We will use loops for these purposes in subsequent analyses, but will cover the basics of them for now.

When the shell sees the keyword `for`, it knows to repeat a command (or group of commands) once for each item in a list. 
Each time the loop runs (called an iteration), an item in the list is assigned in sequence to the **variable**, and 
the commands inside the loop are executed, before moving on to  the next item in the list. Inside the loop, we call for 
the variable's value by putting `$` in front of it. The `$` tells the shell interpreter to treat the **variable**
as a variable name and substitute its value in its place, rather than treat it as text or an external command. In shell programming, this is usually called "expanding" the variable.

~~~
$ cd ../untrimmed_fastq/
~~~
{: .bash}

Let's write a for loop to show us the first two lines of the fastq files we downloaded earlier using Nano. 

~~~
nano forloop.sh
~~~
{: .bash}

You should see something like this: 

<a href="{{ page.root }}/fig/02-05-01.png">
  <img src="{{ page.root }}/fig/02-05-01.png" alt="nano screen with the name of the file in the top bar, a blank screen to write in the middle, and a bottom bar with the shortcuts for the available nano instructions." />
</a>
<em> Figure 1. GNU Nano Text Editor Menu. <em/>

The structure of a basic for loop is: 


<a href="{{ page.root }}/fig/forloop1.png" >
  <img src="{{ page.root }}/fig/forloop1.png" alt="forloop1.png" />
</a>

<a href="{{ page.root }}/fig/forloop2.png" >
  <img src="{{ page.root }}/fig/forloop2.png" alt="forloop2.png" />
</a>

~~~
for filename in *.fastq
do
head -n 2 ${filename} >> seq_info.txt
done
~~~
{: .bash}

Breaking it down:

    for filename in *.fastq:
        This is a loop that goes through all files in the current directory with a .fastq extension (e.g., sample1.fastq, sample2.fastq).
        Each file name is assigned to the variable filename one at a time during the loop.

    do ... done:
        Everything between do and done is the set of commands that will run for each file in the loop.

    head -n 2 ${filename}:
        The head command reads the first n lines of a file.
        Here, head -n 2 ${filename} reads the first 2 lines of the file named in the filename variable (e.g., sample1.fastq).

    >> seq_info.txt:
        The >> operator appends the output (the first 2 lines) to the file named seq_info.txt.
        If seq_info.txt doesn't exist, it will be created.
        If it already exists, the new lines will be added to the end without overwriting the existing content.

To see the content of the little file we just made it is useful to use the `cat` command.

~~~
cat seq_info.txt
~~~
{: .bash}
~~~
@MISEQ-LAB244-W7:91:000000000-A5C7L:1:1101:13417:1998 1:N:0:TCGNAG
CTACGGCGCCATCGGCGNCCCCGGACGGTAGGAGACGGCGATGCTGGCCCTCGGCGCGGTCGCGTTCCTGAACCCCTGGCTGCTGGCGGCGCTCGCGGCGCTGCCGGTGCTCTGGGTGCTGCTGCGGGCGACGCCGCCGAGCCCGCGGCGGGTCGGATTCCCCGGCGTGCGCCCCCCGCTCCGGCTCGAGGACGCCGCACCGACGCCCCACCCCCCCCCCCGGTGGCTCCTCCTGCCGCCCTGCCTGATCC
@MISEQ-LAB244-W7:91:000000000-A5C7L:1:1101:13417:1998 2:N:0:TCGNAG
CGCGATCAGCAGCGGCCCGGAACCGGTCAGCCGCGCCNTGGGGTTCAGCACCGGCNNGGCGAAGGCCGCGATCGCGGCGGCGGCGATCAGGCAGCGCAGCAGCAGGAGCCACCAGGGCGTGCGGTCGGGCGTCCGTTCGGCGTCCTCGCGCCCCAGCAGCAGGCGCACGCCAGGGAATCCGACCCGCCGCCGGCTCGGCCGCGTCNCCCGCNCCCGCCCCCCGAGCACCCGNAGCCNCNCCACCGCCGCCC
@MISEQ-LAB244-W7:156:000000000-A80CV:1:1101:12622:2006 1:N:0:CTCAGA
CCCGTTCCTCGGGCGTGCAGTCGGGCTTGCGGTCTGCCATGTCGTGTTCGGCGTCGGTGGTGCCGATCAGGGTGAAATCCGTCTCGTAGGGGATCGCGAAGATGATCCGCCCGTCCGTGCCCTGAAAGAAATAGCACTTGTCAGATCGGAAGAGCACACGTCTGAACTCCAGTCACCTCAGAATCTCGTATGCCGTCTTCTGCTTGAAAAAAAAAAAAGCAAACCTCTCACTCCCTCTACTCTACTCCCTT
@MISEQ-LAB244-W7:156:000000000-A80CV:1:1101:12622:2006 2:N:0:CTCAGA
GACAAGTGCTATTTCTTTCAGGGCACGGACGGGCGGATCATCTTCGCGATCCCCTACGAGACGGATTTCACCCTGATCGGCACCACCGACGCCGAACACGACATGGCAGACCGCAAGCCCGACTGCACGCCCGAGGAACGGGAGATCGGAAGAGCGTCGTGTAGGAAAGAGTGTAGATCTCGGTGGTCGCCGTATCATTAAAAAAAAAAAGCGATCAACTCGACCGACCTGTCTTATTATATCTCACGTAA
~~~
{: .output}

The for loop begins with the formula `for <variable> in <group to iterate over>`. In this case, the word `filename` is designated 
as the variable to be used over each iteration. In our case `JC1A_R1.fastq` and `JC1A_R2.fastq` will be substituted for `filename` 
because they fit the pattern of ending with .fastq in directory we've specified. The next line of the for loop is `do`. The next line is 
the code that we want to execute. We are telling the loop to print the first two lines of each variable we iterate over and save the information
to a file. Finally, the word `done` ends the loop.

Note that we are using `>>` to append the text to our `seq_info.txt` file. If we used `>`, the `seq_info.txt` file would be rewritten
every time the loop iterates, so it would only have text from the last variable used. Instead, `>>` adds to the end of the file.


## Making the script into a program

We can now turn this script into its own program. We need to tell it that it is a program by making it executable. We can do this by changing the file permissions. We talked about permissions in [an earlier episode](https://carpentries-incubator.github.io/shell-metagenomics/02-the-filesystem/index.html).

First, let us look at the current permissions.

~~~
$ ls -l forloop.sh
~~~
{: .bash}

~~~
-rw-rw-r-- 1 pdrodrig  staff 72 forloop.sh
~~~
{: .output}

We see that it says `-rw-r--r--`. This combination shows that the file can be read by any user and written to by the file owner (you). We want to change these permissions so the file can be executed as a program. We use the command `chmod` as we did earlier when we removed write permissions. Here we are adding (`+`) executable permissions (`+x`).

~~~
$ chmod +x forloop.sh
~~~
{: .bash}

Now let us look at the permissions again.

~~~
$ ls -l forloop.sh
~~~
{: .bash}

~~~
-rwxr-xr-x  1 pdrodrig  staff  72 Jan  6 16:32 forloop.sh
~~~
{: .output}

Now we see that it says `-rwxr-xr-x`. The `x`'s there now tell us we can run it as a program. So, let us try it! We will need to put `./` at the beginning, so the computer knows to look here in this directory for the program.

~~~
$ ./bad-reads-script.sh
~~~
{: .bash}

We have now created our own computer program!

