# CoreMeta-Writeup
I keep finding this script (Meta) and similar variations of it that impersonates popular developer Quenty, on further investigation it appears to be a backdoor.

# Context
I was working on my own ROBLOX game looking through the toolbox, where I found a tent model that seemed reputable by the user "preppyJ33ssy"

# Methodolgy
I was first alerted to the potential backdoor by the GameGuard antivirus plugin which detected the following: "return CoreMeta:Main() and require(script.Meta)"
![Screenshot_20250709_215000](https://github.com/user-attachments/assets/f80492d3-8602-431e-a34f-e852d204e2ef)
The "require()" function is used to send and recieve items from a ModuleScript, but it can also be abused to remotely send and recieve malicious code, and backdoor games.

# Investigating
The code itself doesn't make sense, it calls for multiple utility functions, (Clamp and FormatData), but they aren't used for anything in particular.
![Screenshot_20250709_215542](https://github.com/user-attachments/assets/b23d1646-0315-4d24-bcae-832415974185)

The Function CoreMeta:Getchildren() loops to find an item named "ketp", and if it doesn't, the function loops infinitely, causing a stack overflow (to crash or lag the game?)
![Screenshot_20250709_215652](https://github.com/user-attachments/assets/8a6b061f-7390-49e2-bc73-690d3d259f6c)
If the GetChildren() function returns true, it'll then run require(script.Meta).

# Upon closer inspection
While the script itself already throws some red flags, parented to the script is a Confiuration object with the following attributes:
![Screenshot_20250709_220046](https://github.com/user-attachments/assets/02bd960a-d709-46b3-bd5c-b1b76b25a287)

They appear to be keys, which are decoded in the main script.
![Screenshot_20250709_220306](https://github.com/user-attachments/assets/fe573b14-6351-4f3a-b63b-c9e51abdc7af)

After sorting, we get this:
![Screenshot_20250709_220412](https://github.com/user-attachments/assets/fa30e562-cefd-4dbe-86a0-9edc55ac9cb3)

It seems to be fetching the description field from a certain product on the ROBLOX marketplace, but why?

# The product.
After plugging the product ID into the searchbar, we get this product page.
![Screenshot_20250709_220632](https://github.com/user-attachments/assets/d8bb7c2a-54fe-49f4-9333-d2c2081c446d)

The owners are DeveIoper-TooIs, a group consisting of 12 members.
The odd looking string, (v#J#<a) looks like it's some sort of payload that is mean to be decoded during runtime.
The title of the product, "newcclosure", is an exploiting function used to hook metatables, most commonly found in Synapse X or KRNL, which were both very popular free script injection software. (SynapseX was removed)
https://devforum.roblox.com/t/newcclosure-problem-error/2782735

I decided to insert the model, where a we find a ModuleScript.
![Screenshot_20250709_221559](https://github.com/user-attachments/assets/507a3bf5-c369-4cde-9ec4-d07934c4b529)

Piecing everything together, the newcclosure module exists to obfuscated and protect the execution of malicious code from being detected.
The scripts allows the actors to remotely send payloads with no more effort required, and isn't detectable within play testing in a Studio environment

# Testing
Inside of a sandbox game, I inserted the free model containing the backdoor script, which leads to the following results:
![Screenshot_20250709_222448](https://github.com/user-attachments/assets/2840a994-b965-4644-95ae-6b9f0959d7e4)
![Screenshot_20250709_222645](https://github.com/user-attachments/assets/28b5ff8f-a6e8-4fbc-875d-fe7e9540ee0f)

It fetches another asset, but whatever it seems to be doing, is causing a lot of network back-and-forth.
Luckily for me, it this asset leads to a [Content Deleted] Product
![Screenshot_20250709_222937](https://github.com/user-attachments/assets/55bde199-877a-4d1e-bb2a-543706700ec2)
You might be thinking I got lucky, and this asset was already moderated... but unfortunately, it leads to an even deeper rabbit hole.

# Part 2
The asset itself is another ModuleScript with a few normal ROBLOX tools parented to it (Lazer Gun, Gravity Coil, fun stuff galore)
Most notably, the script itself contains a jumbled and obfuscated mess of code, nearly 2000 lines long, but one hint is the following text:

![Screenshot_20250709_223521](https://github.com/user-attachments/assets/7d94944a-ce1a-4883-9d7c-d8c4ad8e9969)

Digging around on google brings up a WeAreDevs website (notorious hacking tool website for ROBLOX) where the following is revealed:
![Screenshot_20250709_223742](https://github.com/user-attachments/assets/00638e9c-8c7e-403a-a8c4-00590ea5c7fd)









