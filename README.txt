Configuration Management and Orchestration
    Abstraction of activities
    Idempotent actions
    
Preparation
  Environment Setup
    https://www.vagrantup.com/
    
    F:\Code\devops\udemy-mastering-ansible\mastering-ansible\S2
    
    Topology.pdf
    Vagrantfile
  
  vagrant plugin install vagrant-hostmanager
  vagrant up
  (vagrant halt) for stopping
  
  vagrant ssh control
  cd /vagrant
  mkdir ansible
  
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
        (instructor ansible version 1.9.3)
        
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
            
Files: Copy
    
    Ansible Docs - copy
            http://docs.ansible.com/ansible/latest/copy_module.html
            
    copy module
    
    cd ansible
    webserver.yml
        add copy module
            copy demo app to webserver in new path
                '/' at end demo/app/ is important for not full app dir with path but only contents to be copied
            copy new configuration file to webserver
            restart apache2
            
        
Application Modules: PIP
    
    Ansible Docs - pip
            http://docs.ansible.com/ansible/latest/pip_module.html
            
          
    cd ansible
    webserver.yml
        pip module
            install python libraries and dependencies
            install virtual environment
        notify restart apache2
        
    ansible-playbook webserver.yml


FILES: File
    
    file module
    
    Ansible Docs - file
            http://docs.ansible.com/ansible/latest/file_module.html
            
    file module
        modifies file properties on remote 
        here
            enable python site
            disable apache2 default site

    cd ansible
    webserver.yml
        add file module
            default apache2 site disabled
            python site enabled by symlink
            
    curl app01
    curl app02

    curl lb01
        still will respond to default nginx
        need to change
        
        
FILES: Template
    
    Ansible Docs - template
            http://docs.ansible.com/ansible/latest/template_module.html
            
    template module
        template inplace of copy a file to remote, 
            first supply variable values, render content to remote host
            
    mkdir ansible/templates
    cd templates
    touch nginx.conf.j2
            jinja2 file firmat, nginx conf
         add server loop, other configurations - listen port, location
    
    cd ansible
    loadbalancer.yml
        default nginx site disabled
        demo site enabled
        nginx.conf.j2 template is suuplied to server lb01
        notify restart nginx
        
    curl app01
    curl app02
    curl lb01
    
    
FILES: lineinfile
    
    Ansible Docs - lineinfile
            http://docs.ansible.com/ansible/latest/lineinfile_module.html
            
    connection to database on db01 database host
        ssh db01
            netstat -an 
                local address listening
                127.0.0.1:3306
            nc -zv localhost 3306
                
            not listening on external port
            db connect will fail
            
            vim /etc/mysql/my.cnf
                bind-address 127.0.0.1
                
            copy module, template module, or hardcode changes are option to
                change bind_address
            
            but better is lineinfile module of ansible
                it keeps only changes in the specific lines like
                    bind_address
    
            exit
    
   cd ansible
   database.yml
        add task
            use module lininfile
                change bind-address to listen on all address port 
            notify: restart mysql
        add handler to restart mysql
        
   ansible-playbook database.yml
   
   ansible -a "netstat -an" db01
   
   curl app01/db
        ERROR: No module named MySQLdb
        
   
APPLICATION Modules: Mysql_db, mysql_user

    Ansible Docs - mysql_db
            http://docs.ansible.com/ansible/latest/mysql_db_module.html
    Ansible Docs - mysql_user    
            http://docs.ansible.com/ansible/latest/mysql_user_module.html
            
    curl app01/db
        ERROR: No module named MySQLdb            
            
        some python dependencies to configure
        some configuration on mysql host needed
        
    cd ansible
    webserver.yml
        install 
            python_mysqldb
            
    ansible-playbook webserver.yml
    
    database.yml
        install python-mysqldb
        add mysql_db - create db
        add mysql_user - configure db
        
    curl app01/db
    curl app02/db
    curl lb01/db
    curl lb01
    
    
SUPPORT PLAYBOOK 2: Stack Status, Wait_For
    
    Ansible Docs - wait_for
            http://docs.ansible.com/ansible/latest/wait_for_module.html

    cd ansible/playbooks
    touch stack_status.yml
        command module inplace of service module
        
        wait_for module
            watch ports listening for a time period
    ansible-playbook playbooks/stack_status.yml
            
    stack_restart.yml
        add wait_for module to avoid traffic before a service restarts
            loadbalancer: state drained
                ensure no old connection dropped, and service stopped
            state started is default
        
        
SUPPORT PLAYBOOK 2 - Stack Status: uri, register, fail, when
    
    Ansible Docs - uri
            http://docs.ansible.com/ansible/latest/uri_module.html
    Ansible Docs - register
            http://docs.ansible.com/ansible/latest/playbooks_conditionals.html#register-variables
    Ansible Docs - when
            http://docs.ansible.com/ansible/latest/playbooks_conditionals.html#the-when-statement
    Ansible Docs - with_items 
            http://docs.ansible.com/ansible/latest/playbooks_loops.html#standard-loops
            

    uri module
        interact with services and get to return content for actual status show
        
    cd ansible
        python-httplib2
            useful to get server status from loadbalancer host
            also for status of loadbalancer from control host
        
        loadbalancer.yml
            install python-httplib2
            
        ansible-playbook loadbalancer.yml
            
        control.yml
            install python-httplib2
            
        ansible-playbook control.yml
            
        cd ansible/playbooks
        stack_status.yml
            add hosts: control
                tasks: verify end-to-end response from control to lb and to db,  return content
                with_items: groups.loadbalancer
                set location for returned content to be checked later
                    register module
                        lb_index, lb_db - new variables created
                
                fail module
                    to check earlier saved content in lb_index
        
            add hosts: loadbalancer
                tasks: backend response from lb to servers and to db
                with_items: webserver
                save location variables in register: app_index, app_db
                faile module: check content, item.item, item.content
            
            ansible-playbook playbooks/stack_status.yml
                
            
PLAYBOOKS Summary
    make it work
    make it right
    make it fast
    
    configuration management
        do with confidence in an idempotent manner
            and without bringing down the stack
            
    version controlled infrastructure
    

