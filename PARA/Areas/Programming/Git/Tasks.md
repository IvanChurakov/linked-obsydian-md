## I Can Win

1. Install git and generate a pair of ssh keys. Authorize the public key on github.com.
2. Specify your user.name and user.email in git.
3. Create a new repository on github.com and clone it locally to your computer.
4. Create a file called song.txt and put there half the text of your favorite song.
5. Make a commit called "add first half of my favorite song" and send it to the server.
6. Make sure github has a song.txt file with the lyrics.
7. Using the github's web interface, add the second half of the lyrics and make a commit with the name "finish my song".
8. Make a pull in the local repository and make sure that the commit you created on github is pulled up and you have all the lyrics.

## Bring It On

This task is performed immediately after the previous one (I Can Win).

1. Add a .gitignore file to the project and configure it to hide files with the extension **.db, .log** and directories with the names **target** or **bin**.

2. Create a feature branch and add two commits to it

3. Merge the feature branch in master

4. Return to feature and create the arrows.txt file with the following contents:

`The ship glides gently on the waves   As day turns into night`

Make a commit.

5. Go to master. Create the arrows.txt file there and add the following text:

`One thousand burning arrows`
`Fill the starlit sky`
`Make a commit.`

6. Merge feature in master resolving the conflict: save all 4 lines in arrows.txt file in the order they were added in steps 4 and 5.

## Hurt Me Plenty

This task is performed after the previous one (Bring It On).

1. Create a storm branch and add a commit to the storm.txt file:

`Twenty ships with Norsemen braves`  
`Riding the northern wind`

2. Add 2 more lines to storm.txt and make another commit:

`They left their shores at early dawn   As a red sun was rising in the east`

3. Return to master and create the pursuit.txt file with the text below:

`The warming sun returns again   And melts away the snow   The sea is freed from icy chains   Winter is letting go`

Make a commit.

4. Mark the commit with session1 tag and go to the storm branch.
5. Rebase the storm branch so that it contains the last commit from the master.

## Hardcore

This task is carried out immediately after the previous one (Hurt Me Plenty).

1. Make a push of your repository and make sure all commits are on github.
2. Make a new repository on github.
3. Change the remote repository into the local one so that fetch and push come to to the new repository that was created in the previous step.
4. Make a push and make sure that the second repository on the github looks the same as the first.
5. Return the remote settings to their initial state: pull and push of the first local repository lead to one remote repository on the github.

## Nightmare!

To complete this task, you will need to repeat all 4 previous ones using only the MINGW64 command line (in which git works). You cannot use file managers, GUI text editors, gitk / git gui. You are allowed to use the browser only for github operations. Everything else is only through the console. This is the most difficult task in this course.

Commands that may be useful to you:

`echo`

`cd`

`cat`

`vi`

