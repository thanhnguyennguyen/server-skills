<h1>Notes</h1>

- Bring your SSH key to server
```bash
ssh-add
ssh -A username@server
```

Usage: you SSH to a server, then you want to use your git account to pull/push code on that server.
You should bring your key to the server

- Keep application running on SSH server

  Typically, when we exit from SSH server, the application run by SSH user will be terminated
  We can refer following ways to keep it running on SSH server:
  - [Using Tmux](https://linuxize.com/post/getting-started-with-tmux/)
  - [Using Screen](https://linuxize.com/post/how-to-use-linux-screen/)

- [Stop SSH brute force attempts to your server](https://withblue.ink/2016/07/15/stop-ssh-brute-force-attempts.html)
 
