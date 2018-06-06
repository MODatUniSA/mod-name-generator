# Robot name generator

A Machine Learning algorithm ([RNN & LSTM](https://github.com/jcjohnson/torch-rnn/blob/master/doc/modules.md)) to generate names for MOD.'s robots based on hipster baby names.

This can also be used to generate poetry or any other language based project.

## Software

To make it easy to install and run, this project uses [Cristian Baldi](https://github.com/crisbal)’s [Docker & Torch based image](https://github.com/crisbal/docker-torch-rnn), so that all dependencies are installed for you.

To run the name generator, install [Docker](https://www.docker.com/community-edition#/download).

## Running the Docker image

These instructions are for running it on macOS.

* Open `Terminal.app`
* Start Docker `$ docker-machine start`
* Get the environment setup `$ eval $(docker-machine env)`
* Run the RNN container `$ docker run --rm -ti crisbal/torch-rnn:base bash`

You should now be `root` user in the RNN Docker container and see a prompt something like `root@5a697057ef8a:~/torch-rnn#`

Next we need to copy our name data to the Docker container. Open a new Terminal window, then:

* Find out the name of the running Docker container `$ docker ps`
* Copy your name data over: `$ docker cp hipster-baby-names.txt container_name:/root/torch-rnn/data/my_data.txt` where `container_name` is the name that you see from `docker ps`.
* Switch back to the other Terminal window with the Docker container running.
* Pre-process the name data `$ python scripts/preprocess.py \
  --input_txt data/my_data.txt \
  --output_h5 data/my_data.h5 \
  --output_json data/my_data.json`
* Train the algorithm on that data `$ th train.lua -input_h5 data/my_data.h5 -input_json data/my_data.json -gpu -1`

If you get an error at this step, you may need to change the `batch_size` and `seg_length` to suit the smaller data set. So try: `$ th train.lua -input_h5 data/my_data.h5 -input_json data/my_data.json -gpu -1 -batch_size 8 -seq_length 8` and increase the sizes until you get the error again, and use the values just before it errors out.

Once that’s finished, you’re ready to sample some output from your algorithm to get some generated names!

* Sample output: `$ th sample.lua -checkpoint cv/checkpoint_1000.t7 -length 100 -gpu -1`

Your checkpoint might be named differently to the command above. Check to see what yours is named: `$ ls cv/`

If you’d like to save your sample output back to your computer, use these commands:

* Write the generated names to a file `output.txt`: `$ th sample.lua -checkpoint cv/checkpoint_1000.t7 -length 100 -gpu -1 > output.txt`
* Switch back to the other Terminal window of your computer.
* Copy the file to your computer: `$ docker cp container_name:/root/torch-rnn/output.txt .`

## License

Torch-rnn Copyright (c) 2016 Justin Johnson under an MIT License.

MOD. instructions released under an [MIT License](LICENSE).