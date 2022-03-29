Implementation Details
Background
You are convinced that the homework assignments for this class are not weighted properly. You have a theory that assignments with more words in their description are harder, and therefore should have higher weights. In order to do this you plan to use an R script to analyze the page sizes of each available homework assignment webpage.

To get started you use the course assignments web page. While you could download and analyze each web page individually, you think you may want to test this scheme on multiple classes, so you are going to automate the process using shell scripts. You will write one script to find all the applicable webpages, a second script to obtain data about a single webpage, and then a third script that combines the first two scripts with your R analysis.

Part 1: Extract the assignment URLS from the main webpage
You are going to use this year's course webpage, which you know you can download using wget https://courses.cs.washington.edu/courses/cse374/22wi/assignments/index.html. To run an experiment automatically on each assignment on this page, you need to extract each of the assignment URLs and write them into a text file. There are several ways in which this can be done, and different utilities (sed, grep) can help.

You must use grep and/or sed even if you know other programs or languages (awk, perl, python, ...) that could do similar things in different ways. But it's fine to use egrep and extended regular expressions in sed and grep if you wish.

In a file calledgetpages, write a script that extracts the URLs and writes them into a text file. The script should take two arguments: the name of the output file for results and the name of the input html file. You want to extract ONLY the URLs for homework description webpage. (And, even if you notice a pattern for these webpages you want to rely on your extraction code to retrieve them.)

For example, executing:

    $ ./getpages pagelist index.html
Should write content similar to the following into pagelist:

    https://courses.cs.washington.edu/courses/cse374/22wi/assignments/hw0.html
    https://courses.cs.washington.edu/courses/cse374/22wi/assignments/hw1.html
    ...
If the user provides fewer than 2 arguments, the script should print an error message and exit with a return code of 1.

If the text file provided as argument (for the output) exists, the script should overwrite it with a warning and exit with a 0.

If the html file provided as argument (for the input) does not exist, the script should print an appropriate error message and exit with a return code of 1.

If the script does not report any errors, it should exit with a return code of 0.

Hints: step-by-step instructions

You can view the assignments page in a web browser to see what is there, and if you right-click and 'inspect' you can see which html code is associated with which part of the page.
Use grep to find all the lines that contain the string a href, perhaps in combination with the string Homework. Test if it works (does it return the lines with the Homework links in them?) before proceeding to step 2. You will ultimately pipe the output of this grep search into step 2.
Use sed to edit the lines that are found by grep and remove any text that comes before or after the actual URL. You may also need to add some text to make the results into valid URLs. There are different ways to use sed to do this, but we request that you use a pattern to isolate the URL. (In other words, search for the URL, not any common text that comes before or after it.)
NOTE: You may use a single sedcommand, or multiple commands strung together with pipes.
Test if everything works - does your final list of URLs look correct, or do you need different replacement text?
Part 2: Download a page and compute its size
In a file called measurepage, write a bash script that takes a URL as an argument and outputs the size of the corresponding page in words.

For example, executing your script with the URL of homework 1 on the class website as argument:

$ ./measurepage http://courses.cs.washington.edu/courses/cse374/22wi/assignments/hw1.html
should output only 1319 to standard output:

1319
(This number was correct at the time this assignment was prepared, but might be somewhat different if the page is modified some time in the future.)

If the user does not provide any arguments, the script should print an appropriate error message and exit with a return code of 1.

If the user provides an erroneous argument or if downloading the requested page fails for any other reason, the script should simply print the number "0" (zero). In this case, or if the page is downloaded successfully, the script should exit with a return code of 0 after printing the number to to standard output.

Hints:

The wget program downloads files from the web. Use man wget to see its options.
Your script may create temporary files if you want. The mktemp program produces unique file names for temporary files. If you create a temporary file, you should remove it before your script exits. Generally it is best to create temporary files like this in /tmp.
You should use your wordcount executable from HW3 to count the number of words in each downloaded file. You can copy the executable into the same directory as your HW4 scripts. (If you do not have a working copy of wordcount, you may use this one.
You will be asked to add the current directory to your path, so you will be able to call wordcount instead of ./wordcount
To suppress the output of a command, try to redirect its output to /dev/null. For example try ls > /dev/null
Part 3: Run the experiment by measuring each webpage
To perform the experiment, your need to execute the script measurepage on each URL inside the filepagelist. Once again, you would like to do this automatically with a script.

In a file called runanalysis, write a shell script that:

Writes its output to a file. The name of that file should be given by the user as the first argument.
As usual, if the appropriate files are not provided as arguments, print an appropriate error message and exit with a 1.
If the user provides fewer than 2 arguments, the script should print an error message and exit with a return code of 1.
If the text file provided as argument (for the output) exists, the script should overwrite it with a warning and exit with a 0.
Takes a file with a list of URLs as the second argument and executes measurepage on each URL in the file.
For each URL, runanalysis should produce the following output, separated by spaces: homework-number page-size
The homework-number is the numerical homework designation. You can extract this from the URL you give to runanalysis using a similar method to the one you used to parse the original homework URLS. You should extract only the numerical designation and ignore any letter extensions.
The page-size is the result of measurepage.
Because it can take a long time for the experiment to finish, your script should provide feedback to the user. The feedback should indicate the progress of the experiment.
Before executing measurepage on a URL, your script should print the following message: "Performing wordcount measurement on <URL>...".
Once measurepage produces a value, if the value is greater than zero, the script should output the following message: "...successful". If the value is zero, this means some error has occurred, and the script should output the following message: "...failure".
When run-anaylsis finishes, it should exit with a return code of 0.
Since the course page is relatively small, you should be able to debug your scripts by working with the actual pages for this year. However, it will be useful to set up a test case for error checking. For example:

$ echo https://courses.cs.washington.edu/courses/cse374/22wi/assignments/hw0.html > testurls
    
$ echo https://courses.cs.washington.edu/notaurl >> testurls
    
$ ./runanalysis dataout testurls
  Performing word count measurement on https://courses.cs.washington.edu/courses/cse374/22wi/assignments/hw0.html...
  650
  ...successful
  Performing word count  measurement on https://courses.cs.washington.edu/notaurl...
  0
  ...failure
And the content of dataout should be similar to the ones below. Note that the exact values will change as we edit the class website!

0 650
1 1304
2 1401

Part 4: Put all the parts together and generate a plot
It is hard to understand the results just by looking at a list of numbers, so you would like to produce a graph. More specifically, you would like to produce a scatterplot, where the x-axis will show the course number and the y-axis will show the size of the index page.

Luckily, you've used R for some of your statistics courses, so you find a script called scatterplot.R (Right click to download scatterplot.R). Note that this script expects your experimental results to be stored in a file called dataout, so you'll need to name your output file accordingly.

You will procude a plot by calling the R script at the command line. You can make sure your system has R installed by typing R --version at your command prompt. If it is not installed, you will may need to install it.

The script should produce a file called scatterplot_out.jpg. You can view this file with any image viewer.

Part 5: Write-up
You will want to submit one more file, by following these steps

Start a new transcript: script hw4.script
Enter a command to add the current directory (.) to your PATH
Enter echo $USER or echo $USERNAME (depending on your system).
You can test this by trying to call a script without the ./ in front of it's name
Enter echo $PATH
In order, execute the commands to run through a complete analysis once the scripts are done
This should be around four lines
exit to stop recording your steps.
Re-open the hw4.script file in a text editor, and add the following info:
A brief statement about any trends you see in the data based on the scatter plot. Do these trends allow you to draw any conclusions?
Notice that the R script also prints a series of numbers to the terminal when it runs. Include a brief description of what these numbers represent. (You will want to look a the R script itself for help with this.)
