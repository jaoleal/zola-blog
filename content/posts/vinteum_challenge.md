+++
title = "I did the challenge(again)!"
date = "2024-09-25"
description = "Vinteum launched a new program to fund open-source developers in Brazil, and to get involved, there's a challenge to tackle. So, how do you solve this challenge? And what does it show about a programmer?"
[taxonomies]
tags = ["Vinteum", "opensource", "development"]
+++

Hi, again! Vinteum, a Brazilian org that supports open-source developers in Brazil, is releasing its newly program to financiate open-source developers that are starting their careers... Like me! They made a challenge to be able to participate in their programs and i had some thoughts while completing it.

I'll focus on the challenge here, but if you're interested in the program, you can check more [here](https://vinteum.org/bdl/).

# The challenge itself.

Soon after you subcribed to the program, theyll send an email with a link to a github classroom repository which will automatically create a fork of their challenge and a README.md that describes everything that you need to do. And basically you have to download, compile the **given version** of the bitcoin core, run their functional tests and make only one fail. 

Its not the first time that i did this exactly challenge, at the beginning of the year, [chaincode](https://chaincode.com/) was offering a similar program where they teach you something and after that, they help you monetize what you learn... basically. Chaincode used the same challenge to select the right developers to maintain in the program and thats because this challenge forces the developer to demonstrate some of the fundamentals required to be a functional developer:

- Finding

- Learning 

- Interpret

Compiling things by the source is a tricky task since it`ll put your whole environment to work so, if you dont know the base of bash commands in linux and you arent in a linux distro you can sit in ![Fetal position](https://www.sleepfoundation.org/wp-content/uploads/2021/08/Fetal-Sleep-Position.jpg) and cry for some hours, after you finished it, go learn and install (someway) linux, the basics are more than enough to follow this blog post and complete the challenge itself. A great source to learn is the [Linux Pocket Guide](https://www.amazon.com/Linux-Pocket-Guide-Essential-Commands-ebook/dp/B0CWX5XFQT/ref=sr_1_1?adgrpid=1335907907057957&dib=eyJ2IjoiMSJ9.gj0eVn0OjsjZqFmvHk0_WGuOJAsqGIFAbzTeEPEWEdzkNbathf82jb1lCATZ8H-cfGFe2O3H3dsBZdq-WYj2rKQJaFZ2LLNXSiEj7Kyb7EK5UUP2I-jkoOt1Oi57EosFl22qzECKZlXf8FhEfdvp0tYoP_QZ3Or1ug8VSGWB09bugTWoG9VdFXbegEJAYbWDH5pCcgYXiIWxoHdWXTYCyhvAJ2Nh7WJihNnjeBhLjqs.JabI0l4TMK_dYYqMUx0TMfa6hqIwQG7iD8lNhR7L0Kk&dib_tag=se&hvadid=83494438658036&hvbmt=be&hvdev=c&hvlocphy=116068&hvnetw=o&hvqmt=e&hvtargid=kwd-83495166867026%3Aloc-20&hydadcr=16377_13421596&keywords=linux+pocket+guide&qid=1727287492&s=digital-text&sr=1-1).

## The fundamentals being tested.

**Finding** is a basic concept. You dont have something and you need to have it, so you go for it. Marjoritarianly youll allways deal with something that you dont know 100% being a developer completing any normal task and learning how to go after information is a prime skill to have. Theres a joke in the comunitty saying that developers are only good google searchers and that partially true! Knowing what, when and where to search things can make your life super easy.

**Learning** is another basic concept. You dont have information about something but you need to have, so you go for it! But things in tech allways have in-depth knowledges that can make you loose years learning and still didnt covered everything about it. Learning is not simply to read something and reproduce it later, but yes get what you need and get out of there... Trust me, no one knows everything and the more you know, more you find out things that you dont know(a paradox if you think to much).

Its not every case that will be already answered by someone, the case of this challenge that requests **interpretation** from the dev. Problably youll see things for the first time and you have to assume a lot of what they are and what theyre doing just by superficial information that your brain can get by simply looking at it or having experience with.

you can have another view about how to name these skills but thats what i think and mine maestry and experience with them made the difference between the first and the second time that i did this challenge(The first was not... good).

## Completing the challenge

If you managed to compile the bitcoin core in the right version (v27.1), the next step would run the functional tests written in python found on `test/functional/` with the command `$ python3 test_runner.py` and see if any fails, because if any does you did something wrong while compiling and configuring the source code, if every tests passes, success until here!

I'll suppose that you, someway, learned about commands like `cd`, `ls` and `git`. you can just google them that youre probably find out exactly what you need when you get stuck.

Lets get the challenge done. If you analyze the example in the README.md you can notice by reading the test `feature_abortnode.py`(the one being broke in the example), you can find it on `test/functional/` use the command:

```Bash
    cd test/functional/ # make sure to be in the bitcoin repository!
```

Now we can find that it fails and right in the line 33, the line contains:

```Python
 with self.nodes[0].assert_debug_log(["Failed to disconnect block"])
```

By intuition, its basically searching a certain string in the log pool, the same that the example aplied a diff commenting it and thats why it failed!

Thats the strategy we`ll use to succesfully accomplish the challenge too. Breaking a certain log to fail only one functional test!

Interpreting the line 33 of `feature_abortnode.py` will raise the call of the function `assert_debug_log()` receiving a string to find.

We can use the `grep` tool to find text in files. Let's use the command

```Bash
$ grep "assert_debug_log" *.py
```

to find every python script calling the function. 

Itll pop a lot of them

```Bash
feature_abortnode.py:        with self.nodes[0].assert_debug_log(["Failed to disconnect block"]): #Look, the one of the example. Its a sign.
feature_addrman.py:        with self.nodes[0].assert_debug_log(["Loaded 0 addresses from peers.dat"]):
feature_addrman.py:        with self.nodes[0].assert_debug_log([
feature_anchors.py:        with self.nodes[0].assert_debug_log(["0 block-relay-only anchors will be tried for connections."]):
feature_anchors.py:        with self.nodes[0].assert_debug_log([f"DumpAnchors: Flush 1 outbound block-relay-only peer addresses to anchors.dat"]):
feature_anchors.py:        with self.nodes[0].assert_debug_log([f"Trying to make an anchor connection to {ONION_ADDR}"]):
feature_asmap.py:        with self.node.assert_debug_log(['Using /16 prefix for IP bucketing']):
feature_asmap.py:        with self.node.assert_debug_log(expected_messages(filename)):
feature_asmap.py:        with self.node.assert_debug_log(expected_messages(filename)):
feature_asmap.py:            with self.node.assert_debug_log(expected_messages(self.default_asmap)):
feature_asmap.py:        with self.node.assert_debug_log(
feature_asmap.py:        with self.node.assert_debug_log(expected_msgs=[msg]):

```

You can check the name of the files and the name of the tests that sucessfuly run after compiling the bitcoin core(Not the skipped ones!) and choose one of them, but be carefull while choosing a test to fail, theres cases where more than one test search for the same string. Ill choose the `feature_signet` one to break.

The same function `assert_debug_log` is called in the line 77 on `feature_signet.py`

```Python
 with self.nodes[0].assert_debug_log(["Signet derived magic (message start)"])
```

We can see the String used as a input (`Signet derived magic (message start)`) and a parameter `(message start)`.Parameters can change names so we'll stick with only `Signet derived magic`. In the `README.md` is explained where we need to change the code to break something

```Markdown
2. Write a minimal commit in `src/` (`*.cpp` or `*.h` files only) that makes this one single test fail, and no others!
```
so, lets use more bash commands

```Bash
$ cd ../.. # to come back two directories 

$ cd src # to go to the src/ directory.

$ ls # to list the content of the directory.
```

and a lot of `.cpp` files will pop on your terminal and we already know a tool to find a given text in files. So lets use it to find who is logging out the "Signet derived magic" with the command:

```Bash
grep -r "Signet derived magic" *.cpp # The -r flag is important to make the search recursive.
```
the file `init.cpp` will pop out. So lets change the line 893 
```C++
    LogPrintf("Signet derived magic (message start): %s\n", HexStr(chainparams.MessageStart()));
```
to:
```C++
    LogPrintf(" Sao Paulo !!!!! : %s\n", HexStr(chainparams.MessageStart()));
```

The theory is: Even if the `init.cpp` do all its functions correctly but doesnt log it, the python script cannot assert the execution and will think that something is wrong. And since we made sure to choose a String that is only verified by one python script the next task is to just check if our changes accomplish the challenge annndd...

Success!
```Bash
```