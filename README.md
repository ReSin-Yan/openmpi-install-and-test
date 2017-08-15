# openmpi-install-and-test
Tech How to install openmpi-environmental and simple test

You must have more than 1 computer(The same as vm)

Whatever you use lan or wan , the master computer need to connect the slave computer by ssh with No password login

Follow the step to make the openmpi environment and has the littel test.

# Some necessary installation

As I said , openmpi use ssh to connect . 

So , We need to pre install

~~~
#update
sudo apt-get update && sudo apt-get dist-upgrade -y 

#install ssh
sudo apt-get install ssh
~~~

# Master and slaves network setting

Change name to Master
~~~
sudo vim /etc/hostname 
~~~

Let the Mater can "see" the slaves , set the hosts
~~~
sudo vim /etc/hosts 

#For example
127.0.0.1       localhost
172.20.10.100   Master 
172.20.10.101   Slave1
172.20.10.102   Slave2
...

#After save the hosts , reboot the computer

sudo reboot
~~~

## _slaves_

almost same with the master

Change "all" the slave.



Change name to Slave1(or Slave2... etc.)
~~~
sudo vim /etc/hostname 
~~~

Let the slaves can "see" other , set the hosts
~~~
sudo vim /etc/hosts 

#For example
127.0.0.1       localhost
172.20.10.100   Master 
172.20.10.101   Slave1
172.20.10.102   Slave2
...

#After save the hosts , reboot the computer

sudo reboot
~~~

# Master set ssh for the no password login

In the Master

~~~
cd ~/.ssh 

#Press enter for auto generate key 
#The key is .ssh/id_dsa
ssh-keygen -t dsa

#Set no password login
cat id_dsa.pub >> authorized_keys
~~~

# Send the key to Slave 

In the Master

send key to the Slave

~~~
cd ~/.ssh
scp id_dsa.pub [slave name]@Slave1:~/.ssh/
scp id_dsa.pub [slave name]@Slave2:~/.ssh/
...
~~~

In the Slave

set the key

~~~
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
~~~

Try the no passwd login

~~~
ssh Slave1
ssh Slave2
...

enter exit to leave 
~~~

# Install openmpi

Download on the office website, use the 2.1.1 for example

https://www.open-mpi.org/

wget https://www.open-mpi.org/software/ompi/v2.1/downloads/openmpi-2.1.1.tar.gz

sudo tar -zxvf  openmpi-2.1.1.tar.gz

cd openmpi-2.1.1

#if you want to with cuda (PATH is where cuda install)
./configure --with-cuda=/usr/local/cuda-8.0 --prefix=/usr/local
#without cuda
./configure
sudo make all install 


## _set mpi path_

~~~
sudo vim ~/.bashrc

or 

sudo vim ~/profile

#add follow line

export PATH=/usr/local/bin
export LD_LIBRARY_PATH=/usr/local/lib

source ~/.bashrc

or

source ~/profile

~~~

# Complier and Run test

You can use any Compiler , like nvcc or g++

~~~
nvcc  mpi.cu -lmpi 
~~~

## _Run_

Use mpirun 

You can specify the machinefile or the name

The machinefile is 
~~~
Master
Slave1
Slave2
Slave3
Slave4
...
~~~

Run

~~~
#Name
mpirun --host Master,Slave1,Slave2,Slave3 -mca plm_rsh_no_tree_spawn 1 a.out

or

#machinefile
mpirun -machinefile 2s -mca plm_rsh_no_tree_spawn 1 gafcm
~~~

# Example by the cuda reduction with openmpi
