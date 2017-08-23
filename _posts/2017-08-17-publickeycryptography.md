---
layout: history
type: Research
category: Research
tags: [cryptography, PKI, RSA]
title: Public Key Cryptography
group: 20th Century
timeline: 1980's - today
---

Public key cryptography as first conceived by Diffie and Hellman in 1976 was simple: Alice encrypts a message meant for Bob with his public key and then Bob decrypts it with his private key. This allows secure communication between Alice and Bob since only Bob can decrypt the message intended for him, but anyone can send a secure message to Bob. However, Diffie and Hellman overlooked the more complex problem of key management. They postulated that a simple public key file, listing users’ keys next to their names and addresses and stored in a public directory, would be sufficient. Unfortunately, this solution is both difficult to scale and overlooks the critical concept of authentication. 

<h2>Encryption</h2>
In general, cryptographic algorithms are either symmetric or asymmetric. Symmetric algorithms use the same key (referred to as the “secret key”) to both encrypt and decrypt a message. Asymmetric algorithms, on the other hand, use two keys, a public key for encryption and a private key for decryption. Most symmetric algorithms require less computational power to encrypt and decrypt messages than asymmetric, adding less overhead to the communication process. This is due to the underlying mathematics used for these algorithms causing the entropy of the key to be less than the key length. Entropy refers to the “randomness” of a key. A low entropy means that the attacker can use portions of the key to reduce the set of potential guesses required to break the encryption. Secret keys generally aren’t concerned about entropy since the randomness is built into the symmetric algorithm and the key is not shared publicly. This means that in order for a key for an asymmetric algorithm to have the same entropy (or strength) as a key for a symmetric algorithm, the key length must be much larger. 

Unfortunately, key management for symmetric encryption is difficult. Since the same key is used for both encryption and decryption, it is imperative that the secret key remains private to protect the confidentiality of any communications it is used to encrypt. If an entire encryption configuration was based solely on symmetric encryption, every user pair would require a unique secret key to prevent others from listening in on their conversation. This means that if a system has <i>n</i> users, it would require <i>n x n</i> keys in order to establish a secure network between all of them. In addition, each pair of users would require a secure out-of-band channel to share their secret key (as shown in Figure 1). 

<img src="/images/posts/2017-08-17/AliceBobSymmetric.png" alt="Alice and Bob using symmetric encryption"/>
<div class="img_caption">Figure 1: Alice and Bob need a secure channel to share the secret key used for symmetric encryption</div>

A solution to this problem, as introduced by Diffie and Hellman, is using asymmetric encryption to establish the out-of-band channel. If Bob has both a public key and a private key, Alice can select a temporary secret key (x) at random, encrypt it with Bob’s public key and send it to Bob. Bob then decrypts the message with his private key, known only to him, to get the secret key. From then on, both Alice and Bob can communicate securely with the secret key for a single session (a new secret key will need to be generated and shared for their next session). These steps are depicted in Figure 2 below.

<img src="/images/posts/2017-08-17/AliceBobAsymmetric.png" alt="Alice and Bob using asymmetric encryption"/>
<div class="img_caption">Figure 2: Alice uses asymmetric encryption to share the secret key (x) with Bob</div>

This implementation reduces the number of keys needed to be managed to just 2 keys for each user, or 2<i>n</i> keys in total. This is much more manageable, however, now a new problem of sharing public keys arises. While public keys do not need to be kept confidential like secret keys, they do require authentication. Without authentication, the communications between Alice and Bob are susceptible to a “man-in-the-middle” (MITM) attack. In this case an attacker, Eve, inserts herself between Alice and Bob. When Alice looks up Bob’s public key to determine how to encrypt the secret key being sent to Bob, Eve intercepts the message and replaces the key with her own public key. Alice then encrypts the secret key with Eve’s public key instead of Bob’s. This allows Eve to decrypt the message with her private key, save off the secret key, re-encrypt Alice’s message with Bob’s public key and send it to Bob (see Figure 3 below). Alice and Bob then both switch to communicating with just the secret key and Eve is able to eavesdrop on their entire session. To avoid this attack, Bob needs to be able to authenticate that the public key truly belongs to Alice.

<img src="/images/posts/2017-08-17/EveMITM.png" alt="Eve performing MITM on asymmetric encryption"/>
<div class="img_caption">Figure 3: Eve performs MITM attack, tricking Alice to use her public key instead of Bob’s</div>

<h2>Signatures</h2>
In order for Bob to authenticate that the message came from Alice, Alice can generate a digital signature to send with her message using the process shown in Figure 4. She first hashes the plaintext message she is sending to Bob and then uses a digital signature algorithm to encrypt the resulting hash with her private key. Next she encrypts the plaintext and signature with Bob’s public key and sends them to Bob. From here, Bob can confirm the message truly came from Alice by first decrypting the message with his private key to get the plaintext and the signature. He then generates a hash of the message and decrypt’s Alice’s signature with her public key. If the hash value matches the decryption of the signature, Bob knows Alice sent the message. Since only Alice knows her private key, this is proof that Alice sent the message. This process has the added benefit of not just providing authentication but an integrity check on the message as well. If the message has been changed or altered in some way during transmission, the signature verification will fail. 

<img src="/images/posts/2017-08-17/AliceBobSignature.png" alt="Alice and Bob use signatures"/>
<div class="img_caption">Figure 4: Alice generates a signature for her message before sending it to allow Bob to verify the sender</div>

Unfortunately, this authentication method is still susceptible to a MITM attack if Eve can convince Bob that her public key is Alice’s public key. If so, Eve can again perform the same attack as depicted in Figure 3, she now must spoof both Alice’s and Bob’s public keys to insert herself successfully in their communications, bypassing the encryption and verification steps.

<h2>Digital Certificates</h2>
At this point solutions like PKI (public key infrastructure) step in to add more complexity to the solution to attempt to block Eve’s attack. In order to validate that Alice’s public key belongs to her, PKI wraps Alice’s public key in a public key certificate, called a digital certificate. This certificate is issued and signed by a trusted third party (TTP) called a certificate authority (CA). The certificate contains at least four fields: the sender's name, the sender's public key, the CA issuer name and the CA issuer’s signature. Based on those fields, the relying party (Bob) first validates that the certificate is legitimate by comparing the CA issuer’s signature with that of the CA issuer’s public key. Once confirmed, the receiver can then verify the sender with the public key included in the certificate. Now the receiver has two signatures to verify, that of the issuer of the digital certificate and then the signature used to sign the message received from Alice with the public key provided by the digital certificate. All of this hinges on the trust placed in the CA that the CA has validated the identity of the sender and is now vouching for that sender (Alice) to the relying party (Bob). 

<h2>RSA</h2>
The most popular public-key cryptography algorithm is RSA, named after its three inventors Ron Rivest, Adi Shamir and Leonard Adleman. In RSA, a public key is based on the product of two large prime numbers. These two numbers are used as the private key. The product of the two numbers is then used to generate the public key. RSA’s security hinges on the difficulty of factoring large numbers needed to be done to derive the private key (the two prime numbers) from the public key. Not only is RSA easy to implement, but it can also be used for both asymmetric encryption and digital signatures making it perfect for PKI. RSA is widely used today, having withstood years of extensive cryptanalysis. 


<h2>References</h2>
Diffie, W. and M. Hellman. "New Directions in Cryptography." <i>IEEE Trans. Information Theory</i>. vol. 22, no. 6, pp. 644-654. 1976. 

Schneier, Bruce. <i>Secrets and Lies: Digital Security in a Networked World</i>. New York: John Wiley, 2000.