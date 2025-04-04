- Learn how to pivot through a network by compromising a **public facing web machine and tunnelling** your traffic to **access other machines** in Wreath's network. 

![[Pasted image 20250402151742.png]]


_There are **two machines on my home network** that host projects and stuff I'm working on in my own time -- **one of them has a webserver** that's port forwarded, so that's your way in if you can find a vulnerability! 
It's serving a website that's **pushed to my git server** from my own PC for version control, then cloned to the public facing server. See if you can get into these! **My own PC** is also on that network, but I doubt you'll be able to get into that as it **has protections turned on**, doesn't run anything vulnerable, and can't be accessed by the public-facing section of the network. 
Well, I say PC -- it's technically a repurposed server because I had a spare license lying around, but same difference._


```
From this we can take away the following pieces of information:

- There are three machines on the network
- There is at least one public facing webserver
- There is a self-hosted git server somewhere on the network
- The git server is internal, so Thomas may have pushed sensitive information into it  
    
- There is a PC running on the network that has antivirus installed, meaning we can hazard a guess that this is likely to be Windows
- By the sounds of it this is likely to be the server variant of Windows, which might work in our favour  
    
- The (assumed) Windows PC cannot be accessed directly from the webserver
```

