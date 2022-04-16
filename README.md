Docker container to run Android Studio (https://developer.android.com/studio/index.html)

## Usage

Run Android Studio with the files in the working directory by typing the following command:[^1]
```bash
docker run -d --rm \
  --privileged \
  -e DISPLAY=${DISPLAY} \
  --device /dev/dri \
  --device /dev/video0 \
  --device /dev/snd \
  --device /dev/kvm \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v /dev/bus/usb:/dev/bus/usb \
  -v /usr/share/X11/xkb:/usr/share/X11/xkb:ro \
  -v ~/.AndroidStudio.java:/home/developer/.java \
  -v ~/.AndroidStudio.config:/home/developer/.config \
  -v ~/.gradle:/home/developer/.gradle \
  -v ~/Android/:/home/developer/Android/Sdk \
  -v "$PWD:/home/developer/Project" \
  --name androidstudio-$(head -c 4 /dev/urandom | xxd -p)-$(date +'%Y%m%d-%H%M%S')  \
chubaoraka/docker-androidstudio
```

Explanation:

- `--privileged` - to have access to USB devices connected to the host .AndroidStudio* folders to keep the data on the host between different instances (like SDK tools, AVDs, etc.)
- Local project directories need to be mounted into the container
- The name can be anything - I used something random to be able to start multiple instances if needed
- The `DISPLAY` variable has to be set before running the previous `docker run ...` command withn one of the following commands:[^2]

>```bash
>export DISPLAY=host.docker.internal$SCREEN
>```

>or

>```bash
>export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}')$SCREEN
>```

>or

>```bash
>export DISPLAY=$(ip route | grep default | awk '{print $3}')$SCREEN
>```

- The `SCREEN` variable above depends on the platform on which the command is run. It can be set a using command similar to one of the following (the actual integer value after the colon may defer depending on your setup):


>```bash
>SCREEN=:0     # Linux
>SCREEN=:0     # WSL on Windows with Xming installed
>SCREEN=:0.0   # WSL on Windows with VcXsrv installed
>```

## Notes

The container has Git installed. It also has Kotlin installed. The container runs as a non-root user but that user is part of the root group to be able to use KVM. When Android Studio starts for the first time it might display a warning about KVM not working but it does actually.

[^1]: More about setting the `DISPLAY` variable further in the document.
[^2]: More about setting the `SCREEN` variable further in the document.
