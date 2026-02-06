
R2(config-line)#transport input ssh
R2(config-line)#exit
R2(config)#username cisco privilege 15 secret class
R2(config)#ip domain-name boan.com
R2(config)#ip ssh version 2
Please create RSA keys to enable SSH (and of atleast 768 bits for SSH v2).
R2(config)#crypto key generate rsa
The name for the keys will be: R2.boan.com
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 2048
% Generating 2048 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 3 seconds)

R2(config)#ip ssh version 2

