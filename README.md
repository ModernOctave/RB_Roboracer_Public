# RB Roboracer

## Setup
Clone the repo
```bash
git clone --recurse-submodules https://github.com/ModernOctave/RB_Roboracer_Public.git
```

> [!NOTE]
> Make sure to use `--recurse-submodules` to clone the submodules as well. If you forgot to do it initially, run the following commands to do it afterwards.
> ```bash
>git submodule init 
>git submodule update
> ```

Build the docker image
```bash
cd docker
make image
```

Run the docker image
```bash
make container
```
