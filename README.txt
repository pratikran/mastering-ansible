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
        
FOUNDATION
    
  INVENTORY Pt1
    http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html
    http://docs.ansible.com/ansible/latest/intro_inventory.html
    
    Populate Target environment
    Inventory
        Static
        Dynamic
    
        Default Inventory
                ansible --list-hosts all
          file
                vim /etc/ansible/hosts
                        Delete pre-defined hosts
                            may interfere with our inventory
        ansible --list-hosts all
                0 hosts
    
   INVENTORY Pt2
     
     F:\Code\devops\udemy-mastering-ansible\mastering-ansible\S2
            Vagrantfile
            mkdir -p ansible/dev
            cd ansible/dev
     In Editor
        create a Inventory File dev-1
            (It can be simple group of host names)
            (It can be groups of different type of servers)
            
       vagrant ssh control
       cd /vagrant/ansible/dev
       ansible --list-hosts all
            0 hosts
       ansible -i dev-1 --list-hosts all
            5 hosts
            
     By default, ansible ssh into hosts
        even on control/workstation host
            totally unnecessary as we work from control host
                so add in dev-1 file,
                        control ansible_connection=local
       
     Order of Inventory file is set in 
            /etc/ansible/ansible.cfg
                inventory = file
            override to local dev/other file
                
                cd /vagrant/ansible/dev
                
                create local ansible.cfg
                    inventory = ./dev-1
        
                ansible --list-hosts all
                        5 hosts
                        
   HOST SELECTION
       Ansible Docs - Patterns
            http://docs.ansible.com/ansible/latest/intro_patterns.html
            
       Commands can be run against a subset of inventory
         using patterns
        
       COMMON PATTERNS
            ansible --list-hosts all
            ansible --list-hosts "*"
            
            ansible --list-hosts loadbalancer
            ansible --list-hosts webserver
            
            ansible --list-hosts db01
            
            ansible --list-hosts "app0*"
            
            ansible --list-hosts database:control
                    in future ":" can be replaced with ","
                    
            ansible --list-hosts webserver[0]
                    used index 
                    
            ansible --list-hosts \!control
                    negation
                    
            
TASKS
    
     Ansible Docs - Running commands (tasks)
            http://docs.ansible.com/ansible/latest/intro_getting_started.html#your-first-commands
     Ansible Docs - ping
            http://docs.ansible.com/ansible/latest/ping_module.html
     Ansible Docs - command 
            http://docs.ansible.com/ansible/latest/command_module.html
    
    Simple Tasks
    
    ansible -m ping all
        ping is a module
    ansible -m command -a "hostname" all
        command module is default
    ansible -a "hostname" all
        same as above
        
    (tasks with key value parameter)
    (tasks have retunr STATUS, return code(rc))
    
    ansible -a "/bin//false" all
        any task returns STATUS, rc of command run on hosts, not for ansible run
        
    CORE Modules
    NOT CORE Modules
    
PLAYS
    TASKS in PLAYBOOKS to WRITE PLAYS    
    
    ANsible Docs - Plays
            http://docs.ansible.com/ansible/latest/playbooks_intro.html#playbook-language-example
    
    YAML
    
    mkdir ansible/playbooks
    cd ansible/playbooks
    
    touch hostname.yml
        edit
        
    PLAYS in PLAYBOOKS
        Write and Run Multiple Tasks in playbooks against any hostname
        
        tasks
            module: command_to_run
         
PLAYBOOK EXECUTION
    
    Ansible Docs - Playbook Execution
        http://docs.ansible.com/ansible/latest/playbooks_intro.html#executing-a-playbook
        
    cd ansible
    ansible-playbook playbooks/hostname.yml
        cmdLine options -a/-m are within playbook now
        argument patterns is within palybook now
        
        returns status of ansible
            but the result of the host commands has to be determined by us
            
    playbook
        tasks
            name
            module
        (task has a name now)


PLAYBOOKS
    
Playbook Introduction
    
    Ansible Docs - Module Index
            http://docs.ansible.com/ansible/latest/modules_by_category.html
    
    
Packages Apt

    Ansible Docs - apt
            http://docs.ansible.com/ansible/latest/apt_module.html
            
    cd ansible
    touch loadbalancer.yml
        nginx
    touch database.yml
        mysql
        
Packages Become

    Running host command without different user than the logged-in user
        eg - sudo user etc
    
     Ansible Docs - Privilege Escalation
            http://docs.ansible.com/ansible/latest/become.html
     Ansible Docs - boolean (Scroll to fourth code block)
            http://docs.ansible.com/ansible/latest/YAMLSyntax.html#yaml-basics
     YAML Docs - boolean 
            http://yaml.org/type/bool.html
            
            
     cd ansible
     ansible-playbook loadbalancer.yml
                ERROR: fatal: [lb01]: FAILED! => {"changed": false, "failed": true, "msg": "Failed to lock apt for exclusive operation"}
                changed=0, failed=1
                
     loadbalancer.yml
        become: true
            superuser priv
     
     ansible-playbook loadbalancer.yml
     
     executing once again completes quicker
        ansible-playbook loadbalancer.yml
            changed=0, failed=0
            
     ansible-playbook database.yml
        become: true
        
        
Packages With_Items
    
      Ansible Docs - Loops (with_items)
            http://docs.ansible.com/ansible/latest/playbooks_loops.html#standard-loops
      Jinja2 Homepage 
            http://jinja.pocoo.org/
            
      cd ansible            
      touch webserver.yml
            to avoid repetition of same lines of code
            to add a list of packages
                apt: name={{item}}
                        jinja syntax
                with_items:
                
Services: service
      
      Ansible Docs - service
            http://docs.ansible.com/ansible/latest/service_module.html
            
      cd ansible
      vim loadbalancer.yml
        add module 
            service
                to start nginx
                
      wget -qO - http://lb01 | less
            
      cd ansible
      touch control.yml
      
      ansible-playbook control.yml
      
      webserver.yml
        similar to loadbalancer.yml, add service for apache2 start and enable
        
        ansible-playbook webserver.yml
        
      database.yml
        similar to loadbalancer.yml, add service for mysql start and enable
        
        ansible-playbook database.yml
         
SUPPORT Playbook 1 - STACK Restart
    
    Operation SUpport
            to keep eye on stack running and operating well
            
    cd ansible/playbooks
        touch stack_restart.yml
        
SERVICES: Apache2_modules, handlers, notify
    
     Ansible Docs - apache2_module
            http://docs.ansible.com/ansible/latest/apache2_module_module.html
     Ansible Docs - handlers, notify 
            http://docs.ansible.com/ansible/latest/playbooks_intro.html#handlers-running-operations-on-change
            
     apache2_module
            need to ensure libapache2-mod-wsgi is enabled, installed earlier
     
            webserver.yml
                add service to enable mod_wsgi 
                  apache2 need restart before this module takes effect
                    add handler for the service to restart apache2
                        service cant be directly used in tasks
                            as service with state=restart will always restart, not conditionally
                    add notify to restart apache2 on the conditional event that mod_swgi is enabled now ie there is a change
                    
                all notify aggregate together
                handlers can be flushed to the end
                
            cd ansible
            ansible-playbook webserver.yml
            
                    
            
                
            
     
        
      
        
      
     

    
    
    
        
            
       
       
                
                
    
  
    
    
    