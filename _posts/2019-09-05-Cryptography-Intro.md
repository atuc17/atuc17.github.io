---
layout: post
title: Introduction to Cryptograph
categories: [cryptography]
---
Mathematics is usually considered "A subject could cause headache with numberous equations". 
Actually, it is =)))) Of course, **cryptography**, a subject based on mathematics, is also complicated.

In fact, cryptography appears around us such as teencode, cryptogram. In information theory, 
cryptography plays an important role in securing data on Internet, protecting privacy or even ransomware
(WannaCry).

**Cryptography** is an area studying how cryptography algorithms and cryptosystems work, and finding ways to invent 
or break cryptosystems. It may be difficult at the beginning (actually it is), but I hope this series could help 
you get access to cryptography and use knowledge to improve or secure your projects.

A **cryptosystem** is like this.

![cryptosystem](/assets/graph.png)

Explain: 
- Plaintext: original message
- Encrypt: the act of making message *unreadable* by using an algorithm, which makes message secure.
- Key 1: used for encryption. The algorithm isn't secure itself, but the key. The algorithm combines the plaintext and the key to produce ciphertext that cannot be read.
- Ciphertext): encrypted message.
- Decrypt: the act of turning ciphertext to plaintext (reverse of encryption).
- Key 2: used for decryption.

For example, if A wants to send a letter with secret message **"meet me at 6:00"** to B, but A is afraid that C may catche the letter and read the message. In this case, A needs to encrypt the plaintext (**meet me at 6:00"**)  to ciphertext (we will discuss how ciphertext is generated in next posts), and send the ciphertext to B. Therefore, even C could catch the ciphertext, C couldn't read it. When B receives ciphertext, B could decrypt it and get the original message.

If key 1 is as same as key 2, the algorithm is called symmetric-key algorithm. Otherwise, it is called asymmetric-key algorithm (or public-key algorithm).

This series will demonstrate how to break cryptosystem in CTF challenges by using knowledge in mathematics and IT. Therefore, it shows that making a good cryptography algorithm is really difficult but really exciting. There are several theories you should learn before starting cryptography. I will try my best to explain these theories in first writeups so that you can follow. Later you can do it by yourself. Basic theories are:
- Euclidean Algorithm / Extended Euclidean Algorithm
- Fermat / Euler theorem
- Chinese Remainder Theorem (how to find solution)
- Matrix

Cryptography is fun! I hope you could enjoy its beauty :)))
