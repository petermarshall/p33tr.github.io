When not to give up, or how not to give up.

The process goes something like this. 
That looks easy enough, there plenty of documentation and lots of people have done it before.
Blindly follow the tutorial you found on the internet.
It fails in a way not documented anywhere.
Give up an look for another solution, or just give up.

Recently I was at a loose end where I work. Current project had stalled. A colleague, senior consultant who had been given the task of setting up a software application so we could asses it called and suggested I take on the task. He also suggested that when I get to the docker install he would like to be involved so he could learn from it. A little strange I thought but hey ho, sure I said. (The implication here is that he had tried to do this install, failed and was passing the task to me, well thats my interpretation).

Install some open source software on a server. Easy no?

There is official documentation and a list of instructions for a windows install and a linux install and many others. In fact there is an overwhelming amount of documentation. Here I realize that this software is not so simple. A database is involved, its PHP, and laravel. The reason there
is lots of documentation is exactly because its not easy.

Also there is the docker option as previously mentioned.

So off I trot to make a windows vm in azure. This doesn't work, lots of reasons, but the main one being the stability of the resulting server. Each time
I create a virtual machine, something goes wrong, either and error message come up, or something doesn't work. Eventually I get a running windows vm, but starting the 
process of installs I realize that running PHP on IIS looks like an unnatural nightmare. So I give up on this route.

Lets go for the Linux option.

I create a Linux vm and start installing all the bits. Following a u tube video of some other guy. What seems evident to him is not quite working for me. So he starts using docker-compose and I realize I don't have docker on this vm. Ok, divert into installing docker. Then I realize I don't have PHP, so I go down that road. Various things don't work and I end up just cutting and pasting things from the internet without understanding what I am doing. Basically I don't know what I am doing in Linux world and I have only got a command line interface to work with.

Time for a break. Chill, consider this is the end of the line. Go for it again.
After some though, I come to the conclusion that the reason the website is not running, might be because I wasn't in the 'home' directory when I installed a bunch of stuff.

Start the whole process again on a fresh built vm. But this time putting code in a subdirectory of the home directory. Eventually I get some signs of success,
or at least no failures. No big red errors. Manage to run a setup process, here there are questions I dont know the answer to, and no clue given as to what the implications of the answers are. Issues around root users and passwords, I dive off again to try to resolve the issues, isn't the internet wonderful.
The setup process completes without errors. But the website url still gives 404. Hmm. Tempted to give up again.
What do you do when everything appears to have run correctly, but the result just doesn't work? 
Re-run the setup, it lets me do this happily, and shows that things have already been installed. Go back to basics. One question, 
enter the url of the website, ok, so lets just put the ip address. Runs through the rest of the setup, and BINGO. Its working.

It took all day, it took multiple fresh vm instances, I re-ran the setup process multiple times, in different places and with different answers.
I even read the documentation. 