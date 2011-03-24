xortool.py
====================

A tool to do some xor analysis:

  - guess the key length (based on count of equal chars)
  - guess the key (base on knowledge of most frequent char)

Usage
---------------------

<pre>
  xortool [-h|--help] [OPTIONS] [&lt;filename&gt;]
Options:
  -l,--key-length     length of the key (integer)
  -c,--char           most frequent char (one char or hex code)
  -m,--max-keylen=32  maximum key length to probe (integer)
  -x,--hex            input is hex-encoded str
</pre>

Example
---------------------

<pre>
# xor is some external script to encrypt files
tests $ xor -f /bin/ls -s "secret_key" > binary_xored

tests $ python ../xortool.py binary_xored
Probable key lengths:
   2:   5.0 %
   5:   8.7 %
   8:   4.9 %
  10:   15.4 %
  12:   4.8 %
  15:   8.5 %
  18:   4.8 %
  20:   15.1 %
  22:   4.7 %
  25:   8.4 %
  28:   4.7 %
  30:   14.9 %
Key-length can be 5*n
Most possible char is needed to guess the key!

# 00 is the most frequent byte in binaries
tests $ python ../xortool.py binary_xored -l 10 -c 00
...
1 possible key(s) of length 10:
secret_key

# decrypted ciphertexts are placed in ./xortool/Number_&lt;key repr&gt;
# ( have no better idea )
tests $ md5sum xortool/0_secret_key /bin/ls
29942e290876703169e1b614d0b4340a  xortool/0_secret_key
29942e290876703169e1b614d0b4340a  /bin/ls
</pre>

The most common use is to pass just the encrypted file and the most frequent character (usually 00 for binaries and 20 for text files) - length will be automatically chosen:

<pre>
tests $ xortool tool_xored -c 20
Probable key lengths:
   2:   5.6 %
   5:   7.8 %
   8:   6.0 %
  10:   11.7 %
  12:   5.6 %
  15:   7.6 %
  18:   5.4 %
  20:   19.8 %
  22:   5.4 %
  25:   7.8 %
  28:   5.7 %
  30:   11.4 %
Key-length can be 5*n
1 possible key(s) of length 20:
an0ther s3cret \xdd key
</pre>

<pre>
tests $ xortool ls_xored -c 00 -m 64
Probable key lengths:
   1:   2.6 %
   3:   3.3 %
   6:   3.3 %
   9:   3.3 %
  11:   7.0 %
  15:   3.2 %
  18:   3.2 %
  22:   6.9 %
  24:   3.3 %
  27:   3.2 %
  30:   3.2 %
  33:   18.4 %
  36:   3.2 %
  39:   3.2 %
  42:   3.2 %
  44:   6.8 %
  48:   3.2 %
  51:   3.2 %
  55:   6.7 %
  57:   3.2 %
  60:   3.1 %
  63:   3.2 %
Key-length can be 3*n
1 possible key(s) of length 33:
really long s3cr3t k3y... PADDING
</pre>

Author: hellman ( hellman1908@gmail.com )

License: GNU General Public License v2 (http://opensource.org/licenses/gpl-2.0.php)