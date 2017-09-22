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
    

ROLES

ROLES OVERVIEW
    
    Ansible Docs - roles
            http://docs.ansible.com/ansible/latest/playbooks_roles.html
            
    Roles
        code reuse for multiple apps
        encapsulation
            to scale infrastructure properly
        
        ansible galaxy
            public role repository
            
    cd ansible
    mkdir roles
    cd roles
    ansible-galaxy init control
    ansible-galaxy init nginx
    ansible-galaxy init apache2
    ansible-galaxy init demo_app
    ansible-galaxy init mysql
    
    
CONVERTING TO ROLES: Tasks, Handlers
    
     Ansible Docs - roles
            http://docs.ansible.com/ansible/latest/playbooks_roles.html
     
     cd ansible
     copy control.yml:tasks to roles/control/tasks/main.yml
     control.yml
        roles: control
     
     ansible-playbook control.yml
            same as previous 
            

     copy database.yml:tasks to roles/mysql/tasks/main.yml
        also,
            interchange tasks for service start and bind_address configuration
                necessary sequence to follow in terms of mysql db service    
     database.yml
        roles: mysql
     copy database.yml:handlers to roles/mysql/handlers/main.yml
        roles: mysql
        
     ansible-playbook database.yml
            same as previous


CONVERTING TO ROLES: Files, Templates

    Ansible Docs - roles
            http://docs.ansible.com/ansible/latest/playbooks_roles.html
            
     copy loadbalancer.yml:tasks to roles/nginx/tasks/main.yml
        also,
            interchange tasks for service start with the last of configuration change task
                necessary sequence to follow in terms of nginx service    
     copy loadbalancer.yml:handlers to roles/nginx/handlers/main.yml
     loadbalancer.yml
        roles: nginx     
        
     ansible-playbook loadbalancer.yml
            same as previous
     
     mkdir ansible/roles/nginx/templates
     move ansible/templates/nginx.conf.j2 to ansible/roles/nginx/templates/
            roles/nginx/tasks/main.yml
                configure nginx site
                    remove 'templates' from the conf file base dir
                
     webserver.yml
     copy tasks to one of these as applicable
            roles/apache2/tasks/main.yml
                    also move apache2 service start to the end
            roles/demo_app/tasks/main.yml
     copy handlers to 
            roles/apache2/handlers/main.yml
            roles/demo_app/handlers/main.yml 
     webserver.yml
        roles: apache2 demo_app          
     
     mkdir ansible/roles/demo_app/files
     move ansible/demo to ansible/roles/demo_app/files/
  

Site.yml: include
    
    Ansible Docs - roles and include
            http://docs.ansible.com/ansible/latest/playbooks_roles.html
            
    cd ansible
    touch site.yml
        2b used to run all playbook in one go
        
        include key
            include yaml for control, database, webserver, loadbalancer
            
    ansible-playbook site.yml
    
VARIABLES: facts
    
    Ansible Docs - facts
            http://docs.ansible.com/ansible/latest/playbooks_variables.html#information-discovered-from-systems-facts
    facts
        check facts variables
            ansible -m setup <HOSTNAME>
    
    roles/mysql/tasks/main.yml
        problem
            bind_address is hardcoded
        dynamic variables
            ansible -m setup db01
                bind_address={{ ansible_eth1.ipv4.address }}
        ansible-playbook database.yml
    
    stack status and restart
        playbooks/stack_status.yml
            verify mysql is listening on port 3306
                wait_for: add host={{ ansible_eth1.ipv4.address }}
        playbooks/stack_restart.yml
                port 3306
                    wait_for: add host={{ ansible_eth1.ipv4.address }}
     
    ansible-playbook playbooks/stack_status.yml
    ansible-playbook playbooks/stack_restart.yml
    

VARIABLES: defaults
    
    Ansible Docs - role defaults
            http://docs.ansible.com/ansible/latest/playbooks_roles.html#role-default-variables
            
    mysql role 
    roles/mysql/tasks/main.yml
        current demo user and database configuration is 
            hardcoded and for an isolated and unshared database
        
        to make generic/standalone/multi-db setup, 
            edit 
                roles/mysql/tasks/main.yml
            to use variables 
                db_name. db_user_name, db_user_pass, db_host_name
                
        roles/mysql/defaults/main.yml
            add above variables key-values
            
VARIABLES: vars
    
    Ansible Docs - variables
            http://docs.ansible.com/ansible/latest/playbooks_variables.html
            
    Variable Precedence
        changes with ansible version
            http://docs.ansible.com/ansible/latest/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable
        
        
    roles/mysql/vars/main.yml
    vars: key/value in tasks
    vars: key/value at play level ie same level as hosts:
    vars: under include: (as in site.yml)
    vars: in play under roles
    
    flat variable hierarchy is better
    
    place vars in predictable place, 1 or may be 2 places
        vars enter global namespace and difficult to maintain if not simple pattern followed
        
        good places
            role defaults
            while instantiating a role
            using a separate file
                vars file
                group vars file - prefferable
                
        keep variables simple
        
     cd ansible
     database.yml
        roles:
          -  { role: mysql, db_name: demo, ...}
          

VARIABLES: with_dict
        
     Ansible Docs - with_dict
            http://docs.ansible.com/ansible/latest/playbooks_loops.html#looping-over-hashes
            
     
     roles/nginx/tasks/main.yml
          
          currently configured
                1 nginx port listening to 2 application server ports
          
          real world scenario:
                more than 1 nginx frontend ports
                    listening to multiple backend application server ports
                    
     with_dict
            roles/nginx/defaults/main.yml
                configure - site app, frontend/backend ports
                
            roles/nginx/tasks/main.yml
                remove reference to 'demo'
                    using with_dict and item.key
            
            roles/nginx/templates/nginx.conf.j2
                replace 'demo' with {{ item.key }} in upstream and location
                        port with {{ item.value.backend }}
                
            ansible-playbook loadbalancer.yml
            ansible-playbook playbooks/stack_status.yml
            


Selective Removal: shell, register, with_items, when  
        
     Ansible Docs - shell
            http://docs.ansible.com/ansible/latest/shell_module.html
     Ansible Docs - register
            http://docs.ansible.com/ansible/latest/playbooks_conditionals.html#register-variables
     Ansible Docs - when
            http://docs.ansible.com/ansible/latest/playbooks_conditionals.html#the-when-statement
     Ansible Docs - with_items
            http://docs.ansible.com/ansible/latest/playbooks_loops.html#standard-loops
                
                     
     hidden problem introduced earlier
            ssh lb01
            ll /etc/nginx/sites-enabled/
                demo
                myapp
                
            demo is there from pre-refactoring phase
                while building configuration management, 
                    we must pay attention to what may have broken
                    things which we meddled with
     
                need to make sure we have total control on folders or configs
                    as here only one site shud be enabled at one time
                    
      conditionals can be used to control such changes
            modules - shell, register, when, with_items
            
            cd ansible
            roles/nginx/tasks/main.yml
                task - get active sites
                    shell, register
                task - de-activate sites
                    file, with_items, when
                    
            ansible-playbook loadbalancer.yml
                demo - show change (removal here)
                myapp - show skipping(remains safe)
                


Variables - continued
    
    refactoring
        demo app role
            interfaces between tiers of app can be parameterized
            path of app can also be parameterized for better management
            
        ansible\roles\demo_app\files\demo\app\demo.wsgi
            parameterize the db credentials
                into a config file
                
                mkdir ansible\roles\demo_app\templates               
                
                move demo.wsgi file to templates
                rename to demo.wsgi.j2
                
        ansible\roles\demo_app\tasks\main.yml
            copy demo.wsgi.js template to web host site 
            
        ansible\webserver.yml
            add parameters in demo.wsgi.j2 to under roles here
            
        ansible-playbook webserver.yml
        
        

VARIABLES: vars_files, group_vars
    
     Ansible Docs - group_vars
            http://docs.ansible.com/ansible/latest/intro_inventory.html#splitting-out-host-and-group-specific-data
     Ansible Docs - vars_files 
            http://docs.ansible.com/ansible/latest/playbooks_variables.html#variable-file-separation
            
     db credentials are at 2 different places
        the names are different
        
        need to place them at a single source of truth variable file
            inventory is one way
                but not to be used here, as it is kept only of hosts list
            vars files
            group vars and host vars structure
                all roles benefits
                    though different files for diferent groups/roles can be created
                variables autmatically picked up
                
                cd  ansible
                mkdir group_vars
                    all yaml file
                        naming sequence coherent to app
                    ansible/database.yml
                        change the db params to substitute values from all yaml
                        remove the global name patterns, as they will be globally available from all.yml
                        
                    ansible/webserver.yml
                        remove db params as they match keys from all yaml
                        just keep demo_app role
                        
                ansible-playbook webserver.yml
                ansible-playbook database.yml
                
                        
                    
 VARIABLES: vault
        
        Ansible Docs - vault
                http://docs.ansible.com/ansible/latest/playbooks_vault.html
        
        modularity and encapsulation
        security of credentials
            till now we have credentials in plain texts
            
        ansible vaults
            vaults will keep credentials encrypted
                supply them to ansible on fly
                
                little loss of visibility of values
                
                little optimal practice would be to maintain a vars file and alternate b/w them for specifics
                
        cd ansible/group_vars
                rename all to vars
                mkdir all
                move vars to all
                
                ansible/group_vars/all/vars
                
         cd ansible/group_vars/all/
            
            export EDITOR=vi
            ansible-vault create vault
                
                enter password twice
                    it opens a file to put credentials
                        this file can be opened using the password
                    this is also a yaml
                    
                    enter key/value as you like
                        vault_db_pass: ******
                            see this key is different than key 'db_pass' in vars file
                                useful to avoid collisions
                        
                    ls -la vault
                        cat vault
                            it is a text with AES256 encrypted values
                            
            to edit
                ansible-vault edit vault
                
            ansible/group_vars/all/vars
                edit to pull value of db_pass from vault
                
            cd ansible
                
                ansible-playbook site.yml
                    ERROR! Decryption failed on /vagrant/ansible/group_vars/all/vault
                    
                it needs vault password, 2 ways to get around
                    
                    1.
                        ansible-playbook --ask-vault-pass 
                            enter vault password 
                    2.
                        cd ansible
                        echo "password" > ~/.vault_pass.txt
                        chmod 0600 !$
                            ansible-playbook --vault-password-file 
                            or better
                            ansible/ansible.cfg 
                                vault_password_file = ~/.vault_pass.txt
                                
                 ansible-playbook site.yml
                 
              ansible vault supports only one vault password during each execution
                    need to distribute the vault password or vault password file for this execution when in large team
                    
              separate playbook for password rotation b/w different group of hosts can be created but
                  find balance b/w create a separate playbook for password rotation 
                        and complication of site.yml
                        
                        


External Roles & Galaxy
        
         Ansible Galaxy
                https://galaxy.ansible.com/
         Ansible Docs - galaxy 
                http://docs.ansible.com/ansible/latest/playbooks_roles.html#ansible-galaxy


         Ansible Galaxy
                Download and use Ansible Contents build by others
                
                How to choose a Role
                    Long around available, lots people used it
                    How much coverage does it have for underlying application
                            difficult to custom a code built by others
                                so, you need flexibility
                    Updates rolling in or not ie new features etc
                    
                    
                    
Advanced Execution Introduction
        
        time ansible-playbook site.yml
            took >6 minutes around
        

Removing Unnecessary Steps: gather_facts
        
        Ansible Docs - gather_facts
                http://docs.ansible.com/ansible/latest/playbooks_variables.html#turning-off-facts
                
        ansible/playbooks/stack_status.yml
            
                add gather_facts: false
                    to relevant play sections (database would be exception for it needs IP address of host main interfaces)
                    
        gather_facts: false
                
                add it all plays(top level yml)
                    database will exception
                    
        time ansible-playbook site.yml
            took >3 minutes around
                    
                
Extracting Repetitive Tasks: cache_valid_time
        
        Ansible Docs - apt
            http://docs.ansible.com/ansible/latest/apt_module.html
        
        
        lot of tasks are waiting for apt execution
                
               update_cache=yes
                    overhead as it update cache after initial install everytime
                    
               apt module
                    cache_valid_time
                        implicitly sets update_cache
                            only when cache is old that this
               
               we can take out update_cache from everywhere and put it as once only act in site.yml
                    then remove from all other playbooks
                    
               site.yml
                    to add task for apt module cache update 
                    
               remove update_cache from all of the tasks inside roles
               
               time ansible-playbook site.yml
                    took <1.5 minutes around
                    
                    
Limiting Execution by Hosts: limit
        
        Ansible Docs - Patterns (limit - near the bottom)
                http://docs.ansible.com/ansible/latest/intro_patterns.html
                
        
        time ansible-playbook site.yml --limit app01
            
            while making changes to playbooks for tackling particular situation
                we can thus run only for partiular host using limit parameter
                


Limiting Execution by Tasks: tags
        
         Ansible Docs - tags
                http://docs.ansible.com/ansible/latest/playbooks_tags.html
                
         tags
              select parts of our playbooks, just few tasks
                horizontal selection across hosts
                
         roles/control/tasks/main.yml
                add tags - packages
                
         ansible-playbook site.yml --list-tags
                shows all tags
                
         ansible-playbook site.yml --tags "packages"
         
         ansible-playbook site.yml --skip-tags "packages"
                to skip a tag
                
         add tags to all tasks under roles
                also site.yml
                
         time ansible-playbook site.yml --list-tags
         
         time ansible-playbook site.yml --skip-tags "packages"
         
         don't add too many logic
         
         
Idempotence: changed_when, failed_when
        
        Ansible Docs - changed_when
                http://docs.ansible.com/ansible/latest/playbooks_error_handling.html#overriding-the-changed-result
                
        PLAY RECAP
            check at the end anything failed, changed
                with colored status
                
            ansible allows to add our own
                
                changed_when
                    driven by command exit status on hosts
                
                changed_when: false
                
                add to command tasks
                    stack_status.yml
                    
                time ansible-playbook playbooks/stack_status.yml
                    
                        all changed is white colored(no change)
                        
            roles/nginx/tasks/main.yml
                
                task: get active sites
                        changed_when: "active.stdout_lines != sites.keys()"
                        
                time ansible-playbook site.yml --limit lb01 --tags "configure"
                
                to test add a second app site to defaults/main.yml dict
                    time ansible-playbook site.yml --limit lb01 --tags "configure"
                    
            failed_when
                not driven by command exit status on hosts
                    


Accelerated Mode and Pipelining
        
        Ansible Docs - Accelerated Mode
                http://docs.ansible.com/ansible/latest/playbooks_acceleration.html
        Ansible Docs - pipelining
                http://docs.ansible.com/ansible/latest/intro_configuration.html#pipelining
                
        ansible default connection mechanism to hosts
            
                SSH
            
        Other ansible approaches
        
            python module
                paramiko

            modern approach
                ControlPersist

            Accerated Mode
                in old versions only <1.5

            Pipelining
            
            
        Pipelining
            
            vi /etc/ansible/ansible.cfg
                ssh section 
                    pipelining = false/true
                    
            instead of global ansible.cfg
                edit local ansible.cfg
                    add 
                        [ssh_connection]
                        pipelining = True
                        
            sudo visudo
                under default section
                    some distribution have
                        you can't sudo if you dont have true TTY
                        
                    this situation is problem for ansible
                        so, we can add defaults for tty not required
                        
                
             check docs and give it a try to
                accelerated mode or pipelining 
                    to test
                    
             
             
Troubleshooting Ordering Problems
        
        mysql tasks
            mysql server start
            mysql configuration change
                    order should be properly thought upon b/w aboce two
                    
        if things go wrong,
            avoid manual twiking till last
                but build ansible playbooks robust
                
        temporarily comment out failed step can be helpful
            
        ignore_errors: true
            can be useful
                use it once
                    after corrected steps run
                        remove it
                        
        trial and error
        
        work through ansible only
            no external approach
            
            
Jumping to Specific Tasks: list-tasks, step, start-at-task
        
        Ansible Docs - Start and Step
            http://docs.ansible.com/ansible/latest/playbooks_startnstep.html
            
        hopping interactively to run individual tasks as wish-list
            
            ansible-playbook site.yml --step
            
            ansible-playbook site.yml --list-tasks
            
            ansible-playbook site.yml --start-at-task "copy demo app source"
            


Retrying Failed Hosts
        
        ansible-playbook site.yml --skip-tags "packages"
        
        ansible-playbook site.yml --limit @/vagrant/ansible/site.retry
        
        
        
Syntax-Check & Dry-Run: syntax-check, check
        
        Ansible Docs - Check Mode
                http://docs.ansible.com/ansible/latest/playbooks_checkmode.html
                
        feedback before real run or deployment
            
            SYNTAX CHECK
            ansible-playbook site.yml --syntax-check
            
            DRY-RUN
            ansible-playbook site.yml --check
            
            inventory file required even if empty
                


Debugging: debug
        
        Ansible Docs - debug
                http://docs.ansible.com/ansible/latest/debug_module.html
                
        
        debug module
        
               helps get values from runtime environment
                    like value of a variable on the fly
                    
               eg
                    nginx tasks
                        
                        - debug: var=active.stdout_lines
                            
                            set this b/w tasks using active.stdout_lines
                            
                        ansible-playbook site.yml --limit lb01 --start-at-task "get-active-sites"
                            
                        - debug: var=db_name
                        
                        ansible-playbook site.yml --limit lb01 --start-at-task "get-active-sites"
                        
                        - debug: var=vars
                        
                        ansible-playbook site.yml --limit lb01 --start-at-task "get-active-sites"
                        
                        
                        
            
        
        
            
        
