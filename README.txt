Configuration Management and Orchestration
    Abstraction of activities
    Idempotent actions
    
Preparation
  Environment Setup
    https://www.vagrantup.com/
    Topology.pdf
    Vagrantfile
    
  Installation
    http://docs.ansible.com/ansible/latest/intro_installation.html
    
    Ansible is python aplication
        can be install using pip
        
    Better is Ubuntu Apt install
        http://docs.ansible.com/ansible/latest/intro_installation.html#latest-releases-via-apt-ubuntu
        
        $ sudo apt-get update
        $ sudo apt-get install software-properties-common
        $ sudo apt-add-repository ppa:ansible/ansible
        $ sudo apt-get update
        $ sudo apt-get install ansible
        
        On older Ubuntu distributions, “software-properties-common” is called “python-software-properties”.
        
    check
        ansible --version
                ansible 2.3.2.0 (tutors version was 1.9.3)
                python 2.7.6
        ansible-playbook --version
                ansible-playbook 2.3.2.0
                python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
        ansible-galaxy --version
                ansible-galaxy 2.3.2.0
                python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
            
        config file = /etc/ansible/ansible.cfg
        
     
        
        
    
    
  
    
    
    
