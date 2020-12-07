#!/usr/bin/env bash

bootstrap_system(){
	echo "We will now install development tools from your distribution sources."
	echo "For this we need to use sudo..."
	echo "You need to enter your sudo password."

	sudo apt update
	sudo apt upgrade
	sudo apt install --no-install-recommends git cmake ninja-build gperf \
	  ccache dfu-util device-tree-compiler wget \
	  python3-dev python3-pip python3-setuptools python3-tk python3-wheel xz-utils file \
	  make gcc gcc-multilib g++-multilib libsdl2-dev

	echo "Using CMAKE version $(cmake --version)"

	echo "Installing west tool..."

	pip3 install --user -U west
	echo 'export PATH=~/.local/bin:"$PATH"' >> ~/.bashrc
	source ~/.bashrc
}

bootstrap_app(){
	PROJECT_DIR=$1
	west init "$PROJECT_DIR"
	cd "$PROJECT_DIR"
	west update
	west zephyr-export
	pip3 install --user -r "$PROJECT_DIR/zephyr/scripts/requirements.txt"
}

bootstrap_sdk(){
	cd ~
	wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.11.4/zephyr-sdk-0.11.4-setup.run
	chmod +x zephyr-sdk-0.11.4-setup.run
	./zephyr-sdk-0.11.4-setup.run -- -d ~/zephyr-sdk-0.11.4
	echo "We will now install openocd udev rules. For this we need to call sudo. Your password is safe!"
	sudo cp ~/zephyr-sdk-0.11.4/sysroots/x86_64-pokysdk-linux/usr/share/openocd/contrib/60-openocd.rules /etc/udev/rules.d
	sudo udevadm control --reload
}

POSITIONAL=()
while [[ $# -gt 0 ]]
do
key="$1"

case $key in
    --sdk)
	shift # past argument
	bootstrap_sdk
    ;;
    --sys|--system)
	bootstrap_system
    shift # past argument
    ;;
    --app)
	export ZEPHYR_BASE=""
	if [ "$2" != "" ]; then
		APP_PATH="$2"
	else
		echo "Usage: --app <path>";
		exit 1
	fi
	bootstrap_app "${APP_PATH}"
    shift # past argument
    shift # past value
    ;;
    *)    # unknown option
    POSITIONAL+=("$1") # save it in an array for later
    shift # past argument
    ;;
esac
done
