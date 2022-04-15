Docker container to run Android Studio (https://developer.android.com/studio/index.html)

## Usage

Run Android Studio with the files in the working directory by typing the following command:
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
chubaoraka/docker-androidstudio:3.4.1.0
```

Explanation:

- `--privileged` - to have access to USB devices connected to the host .AndroidStudio* folders to keep the data on the host between different instances (like SDK tools, AVDs, etc.)
- Local project directories need to be mounted into the container
- The name can be anything - I used something random to be able to start multiple instances if needed

## Notes

The container has Git installed. It also has Kotlin installed. The container runs as a non-root user but that user is part of the root group to be able to use KVM. When Android Studio starts for the first time it might display a warning about KVM not working but it does actually.
