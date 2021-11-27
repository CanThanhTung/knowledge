<h1 alige="center">Tmux Cheat Sheet</h1>

## Table of contents
[1. Sessions](#1)<br>
[2. Windowns](#2)<br>
[3. Panes](#3)<br>
[4. Copy Mode](#4)<br>
[5. Mics](#5)<br>
[6. Help](#6)

## <b id="1">1. Sessions</b>
1. Start new session
    ```
    $ tmux
    $ tmux new
    $ tmux new-session
    : new
    ```
2. Start new session with name
    ```
    $ tmux new -s {session-name}
    : new -s {session-name}
    ```
3. Show all sessions
    ```
    $ tmux ls
    $ tmux list-sessions
    CTRL + b s
    ```
3. Kill/Delete session with name
    ```
    $ tmux kill-ses -t {session-name}
    $ tmux kill-session -t {session-name}
    ```
4. Kill/Delete all sessions
    ```
    $ tmux kill-session -a
    ```
5. kill/delete all sessions but {session-name}
    ```
    $ tmux kill-session -a -t {session-name}
    ```
6. Rename session
    ```
    CTRL + b s
    ```
7. Detach from session
    ```
    CTRL + b d
    ```
8. Detach others on the session (Maximize window by detach other clients)
    ```
    :attach -d
    ```
## <b id="2">2. Windowns</b>
## <b id="3">3. Panes</b>
## <b id="4">4. Copy Modes</b>
## <b id="5">5. Misc</b>
## <b id="6">6. Help</b>