
## SMB Relay Attacks and Overview

SMB Relay Attacks - instead of cracking the hash, we can instead relay those credentials to the target server/client workstations and potentially gain access. 
- The server acts as a man in the middle

### To successfully attack the server:

1. SMB signing must be disabled/not enforced
2. Relayed user credential must be an admin to that machine 