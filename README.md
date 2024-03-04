[![Contributors][contributors-shield]][contributors-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
<br />

<div align="center">
<h3 align="center">DMX4All</h3>
  <p align="center">
    Creates a WebSocket on port 9999 that can control DMX lighting.
  </p>
</div>

## Installing

&nbsp;&nbsp;&nbsp;&nbsp;[Download](https://github.com/NonzeroCornet/DMX4All/releases/tag/v1.0.0) the zip file from the latest release and unzip it wherever you'd like. It's that simple! (Note, this program only works on Windows at the moment! Apologies.)

## Usage

&nbsp;&nbsp;&nbsp;&nbsp;You may have noticed that the main file you're running is a Batch file. The reason is kinda technical, but in order for the DMX Executable to find it's dependencies, it needs to be either run directly, or from the command line based in the same directory. Technically, you can just run the EXE found in the "src" folder, but if you wanted to automatically open DMX4All from a different program, you're going to be saved a lot of headaches running the Batch file instead of the Executable.

&nbsp;&nbsp;&nbsp;&nbsp;Before you begin, plug in a USB to DMX adapter. Pretty much anything that uses FTDI will work fine. Upon running DMX.bat, you should see a small window appear. If it says "DMX", there are no errors with your installation. Feel free to minimize this window if you wish, or move it off to the side. Note that the window is what keeps the process alive. A new WebSocket will have been created on port 9999, and can be connected to via the URL "ws://localhost:9999/dmx". Upon connection, channels may be altered by sending a message structured as "ChannelNumber:Value". Closing the DMX window ends the connection.

## Example

JavaScript:
```javascript
/* DMX4All example: turns on the light on d001 upon space key pressed
(Assumes channel one controls brightness, and channels 2-4 control RGB) */

var websocket;
var tried = false;

function connect() {
  // Create a connection to the DMX WebSocket
  websocket = new WebSocket("ws://localhost:9999/dmx");

  websocket.onopen = function () {
    // Set some values on channels 2-4
    websocket.send("2:255");
    websocket.send("3:255");
    websocket.send("4:255");

    // Reset reconnection variable
    tried = false;
  };

  websocket.onclose = function () {
    // WebSocket connection closed, try to reconnect if we haven't already tried
    if (!tried) {
      console.log(
        "DMX connection is closed. Reconnect will be attempted in 1 second."
      );

      // We have now tried to reconnect
      tried = true;
      setTimeout(function () {
        connect();
      }, 1000);
    }
  };

  // The WebSocket returned an error, abort
  websocket.onerror = function () {
    websocket.close();
    console.log("DMX connection encountered error. Closing socket");
  };
}

// Connect to WebSocket on window load
window.onload = () => {
  try {
    connect();
  } catch (e) {}
};

document.addEventListener("keydown", (event) => {
  // Set Channel 1 value to max upon space key pressed
  if (event.key == " ") {
    websocket.send("1:255");
  }
});

document.addEventListener("keyup", (event) => {
  // Set Channel 1 value to 0 upon space key released
  if (event.key == " ") {
    websocket.send("1:0");
  }
});
```

## Contact

Brian Dean Ullery - @nonzerocornet34 - [ullerbri000@gmail.com](mailto:ullerbri000@gmail.com)<br>
Website: https://briandean.dev/

[contributors-shield]: https://img.shields.io/github/contributors/NonzeroCornet/DMX4All.svg?style=for-the-badge
[contributors-url]: https://github.com/NonzeroCornet/DMX4All/graphs/contributors
[stars-shield]: https://img.shields.io/github/stars/NonzeroCornet/DMX4All.svg?style=for-the-badge
[stars-url]: https://github.com/NonzeroCornet/DMX4All/stargazers
[issues-shield]: https://img.shields.io/github/issues/NonzeroCornet/DMX4All.svg?style=for-the-badge
[issues-url]: https://github.com/NonzeroCornet/DMX4All/issues
