---
title: "The magic behind restoring multiplayer for Battle for Middle-Earth 2 - Part 1"
date: 2023-10-28
seo_title: Restoring Multiplayer for Battle for Middle-Earth 2
seo_description: A writeup on what it takes to restore online functionality for Battle for Middle-Earth 2 in a similar fashion to T3A:Online.
---

[The Lord of the Rings, The Battle for Middle Earth II](https://en.wikipedia.org/wiki/The_Lord_of_the_Rings:_The_Battle_for_Middle-earth_II) is one of many
real-time strategy games released in 2006, yet it's something special, for me at least.
Maybe because it's set in the world of JRR Tolkien or perhaps just because it reminds me of my childhood.
Either way, it's one of the few games that I can motivate myself to write software for.

# The issue with online gameplay

If you've ever tried to play the game in multiplayer, you've probably noticed that **EA has shut down the online servers**.
While the community has found many ways to continue playing against each other, for example via [GameRanger](https://en.wikipedia.org/wiki/GameRanger),
only [T3A:Online](https://t3aonline.net) has managed to restore the game's original online feature.
They achieved this by building their own GameSpy servers and then modified the game to use these instead of the original ones.

As a software developer, I have always wondered how exactly stuff like this works. To my surprise, the source code was not available.
Neither for the t3a online servers, nor for the client side modifications to the game.
The reasoning seemed to be simple: they didn't want the community to fragment any further.
The risk of introducing additional servers that could compete with T3A:Online seemed quite high.
Obviously this was quite disappointing for me as I had no reference to learn from.
But there is another problem with that: the service could simply die if the maintainers lose interest.
Not to mention that it also hinders others in the community from contributing bugfixes and features.

# The solution

A few months ago I stumbled upon [OpenSpy, an open source project](https://github.com/chc/openspy-core-v2) that aims to restore the online functionality for
GameSpy based games. Unfortunately, The Battle for Middle Earth II was not yet supported.
Well, let's [attempt to change that](https://github.com/anzz1/openspy-client/issues/3).

## The client side

It's easier to start with the client side as it's fairly simple to verify by just testing if a connection to the existing T3A:Online servers can be established.
By looking at
how [the online functionality for other games is restored](https://github.com/anzz1/openspy-client/blob/f18d410fc0cfe2e69ec32e93f088209527093749/include/game_cry.h#L90-L95),
I assumed that there's only two things to implement:

1. a hook for the `gethostbyname` function to redirect GameSpy/EA specific DNS queries
2. a patch so that the game skips the certificate validation when it connects to the online servers

I found the hosts that I had to redirect DNS queries for by looking at the `t3aonline.dll` with [Ghidra](https://github.com/NationalSecurityAgency/ghidra),
a software reverse engineering framework created and maintained by the NSA Research Directorate.

[![A screenshot of the decompiled t3aonline.dll](/assets/bfme2-ghidra-t3a-online.png)](/assets/bfme2-ghidra-t3a-online.png)

The screenshot above only shows a few lines of the decompiled function, and it was a real mess to untangle all the nested if statements and loops but here is
what it looks like:

```cpp
hostent *WSAAPI Hooked_gethostbyname(const char *name) {

    if (strcmp("gpcm.gamespy.com", name) == 0) {
        return gethostbyname("gpcm.server.cnc-online.net");
    } else if (strcmp("peerchat.gamespy.com", name) == 0) {
        return gethostbyname("peerchat.server.cnc-online.net");
    } else if (strcmp("psweb.gamespy.com", name) == 0) {
        return gethostbyname("server.cnc-online.net");
    } else if (strcmp("arenasdk.gamespy.com", name) == 0) {
        return gethostbyname("server.cnc-online.net");
    } else if (strcmp("lotrbfme.arenasdk.gamespy.com", name) == 0) {
        return gethostbyname("server.cnc-online.net");
    } else if (strcmp("ingamead.gamespy.com", name) == 0) {
        return gethostbyname("server.cnc-online.net");
    } else if (strcmp("master.gamespy.com", name) == 0) {
        return gethostbyname("master.server.cnc-online.net");
    } else if (strcmp("lotrbme.available.gamespy.com", name) == 0) {
        return gethostbyname("master.server.cnc-online.net");
    } else if (strcmp("lotrbme.master.gamespy.com", name) == 0) {
        return gethostbyname("master.server.cnc-online.net");
    } else if (strcmp("lotrbme.ms13.gamespy.com", name) == 0) {
        return gethostbyname("master.server.cnc-online.net");
    } else if (strcmp("lotrbme2r.available.gamespy.com", name) == 0) {
        return gethostbyname("master.server.cnc-online.net");
    } else if (strcmp("lotrbme2r.master.gamespy.com", name) == 0) {
        return gethostbyname("master.server.cnc-online.net");
    } else if (strcmp("lotrbme2r.ms9.gamespy.com", name) == 0) {
        return gethostbyname("master.server.cnc-online.net");
    } else if (strcmp("gamestats.gamespy.com", name) == 0) {
        return gethostbyname("gamestats2.server.cnc-online.net");
    } else if (strcmp("lotrbme.gamestats.gamespy.com", name) == 0) {
        return gethostbyname("gamestats2.server.cnc-online.net");
    } else if (strcmp("lotrbme2r.gamestats.gamespy.com", name) == 0) {
        return gethostbyname("gamestats2.server.cnc-online.net");
    } else if (strcmp("lotrbme2wk.gamestats.gamespy.com", name) == 0) {
        return gethostbyname("gamestats2.server.cnc-online.net");
    } else if (strcmp("servserv.generals.ea.com", name) == 0) {
        return gethostbyname("http.server.cnc-online.net");
    } else if (strcmp("na.llnet.eadownloads.ea.com", name) == 0) {
        return gethostbyname("http.server.cnc-online.net");
    } else if (strcmp("bfme.fesl.ea.com", name) == 0) {
        return gethostbyname("login.server.cnc-online.net");
    } else if (strcmp("bfme2.fesl.ea.com", name) == 0) {
        return gethostbyname("login.server.cnc-online.net");
    } else if (strcmp("bfme2-ep1-pc.fesl.ea.com", name) == 0) {
        return gethostbyname("login.server.cnc-online.net");
    }

    return gethostbyname(name);
}
```

I quickly added the EasyHook hooking code and then validated that my dll was indeed redirecting dns queries
using [wireshark](https://gitlab.com/wireshark/wireshark/-/tree/master), another great open source tool that allows you to inspect network traffic.

[![A screenshot of wireshark showing successfully redirected dns queries](/assets/bfme2-wireshark-dns-capturepng.png)](/assets/bfme2-wireshark-dns-capturepng.png)

At this point, I was able to view the login mask in game, but it didn't let me connect to the servers as the certificate was still considered invalid.
After searching for a fix for a few days, I accidentally stumbled across [this GitHub repository](https://github.com/Aim4kill/Bug_OldProtoSSL) which
demonstrates a bug in EA's certificate validation based on [the source code for EA's webkit](https://github.com/xebecnan/EAWebkit) used in Need for Speed World.
I managed to locate the exact same bug in The Battle for Middle Earth II.

[![A screenshot of the certificate bug in battle for middle earth 2](/assets/bfme2-ghidra-cert-validation-bug.png)](/assets/bfme2-ghidra-cert-validation-bug.png)

I looked where I could insert a jump instruction so that the game would always think the certificate had an unknown signature.
The easiest way I've found was to insert it in line 228, as shown in the following screenshot:

[![A screenshot of the location of the jump instruction to abuse the certificate bug in battle for middle earth 2](/assets/bfme2-ghidra-cert-validation-bug-jump-instruction.png)](/assets/bfme2-ghidra-cert-validation-bug-jump-instruction.png)

Using [CheatEngine](https://github.com/cheat-engine/cheat-engine) I was able to quickly validate that my change really worked.
I attached myself to the game process and then jumped to `game.dat+00a8d096`, which is the address of the if statement on line 228.
Then I changed the statement so that it jumps to `00A8D0DE`, which is line 237. I was now able to successfully log in to the T3A:Online servers.

Now all that was left was to write code that would do all of this automatically. This is how it looks like:

```cpp
HANDLE currentProcess = GetCurrentProcess();

// address    bytes           assembly instruction
// 00a8d093   89 55 88        MOV        dword ptr [EBP + local_7c],EDX
// 00a8d096   83 7d 88 08     CMP        dword ptr [EBP + local_7c],0x8
// 00a8d09a   74 08           JZ         LAB_00a8d0a4
BYTE search[] = { 0x89, 0x55, 0x88, 0x83, 0x7D, 0x88, 0x08, 0x74, 0x08 };

// jmp 00A8D0DE
BYTE patch[] = { 0xEB, 0x46, 0x90, 0x90 };

BYTE* addressToModify = findPatternInProcessMemory(search, search + 8);
if(addressToModify) {

    SIZE_T bytesWritten;
    bool certPatchSuccessful = WriteProcessMemory(currentProcess, addressToModify + 3, patch, sizeof(patch), &bytesWritten);
    if(!certPatchSuccessful) {
        MessageBoxW(NULL, L"Failed to patch certificate.", L"Error", MB_OK);
    }
}
```

The full version of the
code [can be found here](https://github.com/DarkAtra/bfme2-patcher/blob/daf27730f295be06b931995545b0c1738dd15ec3/game-patcher/src/main/cpp/dllmain.cpp).

Here's a video of the final result:

<video controls style="max-width: 100%;">
    <source src="/assets/bfme2-online-video.mp4" type="video/mp4">
</video>

## The server side

I haven't managed to connect to the OpenSpy servers yet, but I'll post a part 2 as soon as I find something new.
