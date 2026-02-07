# Flutter-Devcontainer
This is a configuration for flutter with devcontainer

### Connect to andrid over wifi

- Go to wireless debugging
- Click Pair device with synchronization code
- With the ip, port and code, execute this command:
  ```cmd
  adb pair <device ip>:<port>
  ```
- With the ip and port, execute:
  ```cmd
  adb connect <device ip>:<port>
  ```
