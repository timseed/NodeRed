# Cluster in NodeRed

I want to explore some data processing options instead of my current QT/C++ processing.

## Why Node-Red ?

I have not used it before


## Requirements

This Node-Red flow needs a Flask Server to be running. It is the flask server that performs the DXCC Lookups. And becuase it is a server it does not have to read/parse the CTY.DAT file every callsign lookup.




## Problems/Thoughts

The only issue I have is that the Python (hamm.dxcc) - from my existing code base gets called with each new record. But this means that the python has to read the CTY.dat file - and load the data each time before a record is processed. 

This is very wasteful. And I suspect will cause issues in WPX/WAE etc
# NodeRed
