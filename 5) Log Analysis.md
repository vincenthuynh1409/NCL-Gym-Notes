# Log Analysis

## Analyze SSH Log File

### SSH (Easy) Write-Up

![[Pasted image 20261009154853.png]]

1. hostname = `myraptor`
2. first IP address to attack the server = `169.139.243.218`
3. second IP address to attack the server = `56.13.188.38`
4. third IP address to attack the server = `30.167.206.91`
5. user was targeted in the attack = `harvey` (poor harvey 🥲)
6. IP address in which the attacker was able to successfully log in = `30.167.206.91`


```
Oct 11 10:36:58 myraptor sshd[30001]: Connection from 30.167.206.91 port 55326

Oct 11 10:36:59 myraptor sshd[30003]: Accepted password for harvey from 30.167.206.91 port 55326 ssh2

Oct 11 10:36:59 myraptor sshd[30005]: pam_unix(sshd:session): session opened for user harvey by (uid=0)
```

