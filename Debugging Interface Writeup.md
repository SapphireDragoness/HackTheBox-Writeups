Debugging Interface Writeup

We are given a file with the .sal extension, which is a dump of asynchronous serial signals from an embedded device.
After a bit of digging, I find a program capable of reading .sal files, called Logic2, which can be found here: https://www.saleae.com/downloads/ .
Let's open up the file.
[screenshot]
Here's the signal. We can analyze it using the "Analyzers" option. We know this is async serial, so let's select that and analyze using the defaults provided.
[screenshot]
We get a bunch of garbled data and framing errors, so something's not right.
By checking the software's documentation (https://support.saleae.com/protocol-analyzers/analyzer-user-guides/using-async-serial), we discover that the reason why our data isn't being read correctly is because the bit rate we're using for our analysis is off.
Measuring the bit rate is easy enough: "Hover your mouse over the fastest 2-bits, and then take the inverse. One way to do this is to find a 2 bits of data that are opposite in state so you can measure the distance between the two transitions and take the inverse of that measurement to get its bit rate."
The inverse of that measurement is roughly 31.000 kHz (30.00 kHz rounded down). Let's edit the bit rate in our analyzer.
[screenshot]
We have readable messages! The last one is our flag. 
