# Writeup-Corridor
A simple writeup for:  
THM: Corridor  
Difficulty: Easy  
Techniques: IDOR, Hashes  
Link: https://tryhackme.com/room/corridor/
____  
Visiting the Attack_IP i find a interractive corridor, where clicking the doors lead to a room.  
<img src="THM_Corridor.png" > 
The challenge description mentions "note the hexadecimal values you find (they look an awful lot like a hash, don't they?)".  
The URL of each room seems to contain a unique hash, so let's save them in a file for dehashing: 

```
echo 'c4ca4238a0b923820dcc509a6f75849b
c81e728d9d4c2f636f067f89cc14862c
eccbc87e4b5ce2fe28308fd9f2a7baf3
a87ff679a2f3e71d9181a67b7542122c
e4da3b7fbbce2345d7772b0674a318d5
1679091c5a880faf6fb5e6087eb1b2dc
8f14e45fceea167a5a36dedd4bea2543
c9f0f895fb98ab9159f51fd0297e236d
45c48cce2e2d7fbdea1afc51c7c6ad26
d3d9446802a44259755d38e6d163e820
6512bd43d9caa6e02c990b0a82652dca
c20ad4d76fe97759aa27a0c99bff6710
c51ce410c124a10e0db5e4b97fc2af39' > hex.hashes
```

I'll use hashcat to dehash, judging by the hash format i would assume it is MD5 hashes:
```
hashcat -m 0 hex.hashes /home/kali/kali-wordlists/rockyou.txt
```
```
c4ca4238a0b923820dcc509a6f75849b:1
c81e728d9d4c2f636f067f89cc14862c:2
eccbc87e4b5ce2fe28308fd9f2a7baf3:3
a87ff679a2f3e71d9181a67b7542122c:4
e4da3b7fbbce2345d7772b0674a318d5:5
1679091c5a880faf6fb5e6087eb1b2dc:6
8f14e45fceea167a5a36dedd4bea2543:7
c9f0f895fb98ab9159f51fd0297e236d:8
45c48cce2e2d7fbdea1afc51c7c6ad26:9
d3d9446802a44259755d38e6d163e820:10
6512bd43d9caa6e02c990b0a82652dca:11
c20ad4d76fe97759aa27a0c99bff6710:12
c51ce410c124a10e0db5e4b97fc2af39:13 
```  
So the doors are numbered 1-13 and as the challenge is about IDOR, let's see if i can find a hidden room by going further than 13 in the URL.
To generate a MD5 hash of the numbers i want to access i'll use `md5sum`:
```
echo -n "14" | md5sum
aab3238922bcc25a6f606eb525ffdc56  -
```
14 came back as `Not Found`, so i will try 15 just to see if 14 might be a decoy to not continue counting upwards:
```
echo -n "15" | md5sum
9bf31c7ff062936a96d3c8bd1f8f2ff3  -
```
15 also came back as `Not Found` so i will assume for now that everything over 15 will come back the same.
So what about 0?
```
echo -n "0" | md5sum
cfcd208495d565ef66e7dff9f98764da  -
```
A non-empty room with a Flag!
