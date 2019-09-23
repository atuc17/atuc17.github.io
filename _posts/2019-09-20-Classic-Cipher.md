---
layout: post
title: "Chapter 1: Classic Cipher"
categories: [cryptography]
---
Cryptography has appeared for along time ago, mostly in war.

In this post, cipher is similar to algorithm, a way to encrypt or decrypt. However, cipher is simpler due to the way of encryption while algorithm is based on mathematics. Now we shall begin <3

# Caesar cipher: the first cipher in the world.

Our English alphabet looks like this

| A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | 
| ----------- | ----------- |
| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 | 25 |

Assume that our message is **CRYPTO**.

Now I will encrypt message by *shifting* each character in message 3 position forward. In our case, it is

C =====> F

R =====> U

Y =====> B (turn back from the beginning)

P =====> S

T =====> W

O =====> R

Therefore, the ciphertext here is **FUBSWR**. Easy, right? :v :v

If you want to decrypt, doing above process with reverse order.

In previous post, I mentioned that a cryptosystem includes plaintext (our above message), ciphertext, **algorithm** and **key**. In this example, the act of *shifting* is our algorithm, and 3 is our key. If you choose another key (4, 5 or 6 or whatever), your ciphertext will be different from mine. You should try it now :D

Now we could write the equation of this algorithm: cipher = (plain + **key**) mod 26 

Explain: modulo 26 in order to turn back to the beginning if we are the end of alphabet.

Cryptography, as I mentioned before, is to protect your data. By using Caesar cipher, you could transfer a message to your friends and if I somehow catch the ciphertext, I couldn't read because I don't have the key to decrypt the ciphertext and get original message.

WAIT A SECOND!!! Is it true that I couldn't read? =)))))

Now we should look back to algorithm, what if key > 25? 

If key = 26, so cipher = (plain + 26) mod 26 = (plain mod 26) + (26 mod 26) = (plain mod 26) + 0 = plain mod 26!!!

What does it mean? It means that key is only from 1 to 25 because of modulo 26. Then, I can try all 25 posible keys and find out what is the true key.

![Caesar breaking](/assets/caesar.png)

As you can see, by trying all 25 keys, I find the 3 will decrypt the ciphertext to **CRYPTO**. Especially, messages decrypted from other keys don't have specific meaning. This is how Caesar cipher was broken.

# Other ciphers

In war, there was also a cryptography battle between inventors and breakers, which decided the winner in many battles in the past. Because Caesar had been broken, new cipher had to be invented to secure data.

There are many algorithms have been invented and many of them have been broken (fully or apart). I suggest you search these ciphers:
- Vigenere cipher
- Playfair cipher
- Hill cipher
- Monoalphabetic Substitution Cipher
- Polyalphabetic Substitution Cipher
- Rail fence cipher
- ......

Perhaps, you have seen one of these ciphers before, in cryptogram or similar games =)))) The problem here is that if we don't know how the message was encrypted, we can't break it. In brief, it is a guesting game.

*The more ciphers you know, the more posibilities you have to break cipher*

Thanks for reading..................

