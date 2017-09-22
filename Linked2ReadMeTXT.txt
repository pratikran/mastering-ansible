Dev Environment with Docker
Section 7, Lecture 55

The environment that I use throughout the course is built using docker and virtualbox. Docker is an application container technology for linux. It's not designed to run full "virtual machines," so even though we're able to hack together a setup to mimic virtual machines, I do not recommend that you use this type of pattern for deploying any real services to production. The reason I like docker for this is that the resulting machines are much more lightweight, and I can run 5 "machines" on my macbook air with 4 GB of RAM (along with a browser, editor, and terminal process). This would be harder to do with a full virtual machine solution such as vagrant. If you have more resources available and want a more standard setup, take a look at vagrant (it's free and open-source).

With that caveat, if you're still interested in getting a similar docker setup going, you can download these tools from their respective sites:

    docker-toolbox
    virtualbox


    Once you install docker-toolbox, you'll need to use docker-machine to get a docker host vm up and running on virtualbox.
    docker-machine create dev -d virtualbox

    With docker-machine up, you can add the connection details to your shell:
    eval "$(docker-machine env dev)"

    Use an ssh keypair generator to create a keypair named ansible:
    ssh-keygen -t rsa -f ansible

    Download the setup files from the attachments on this lecture, unpack the archive to a directory on your machine.
    Copy the keypair you created in step 3 into the env/ subdirectory.
    From the unpacked directory enter:
    docker-compose build && docker-compose up


    In another terminal session (or using an ssh client), connect to the hosts using the ansible key you created, and IP and Port mapping for the instance. Use docker-machine ip dev to print the IP for your docker host; the port mappings are found in the docker-compose.yml. You can also use the provided config file, by moving it to ~/.ssh/config on your local machine and updating the IP to match your docker host. Then you can simply ssh control to access the control host.



Resources for this lecture

    Docker Toolbox
    Virtualbox
    Vagrant
    Docker 
        zip file in S7 directory
        
