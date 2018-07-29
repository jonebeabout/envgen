# User's Manual

Thanks for choosing to use Environment Generator!  Hopefully, it will help you make virtual environments faster and with less headaches.  Please submit an issue if you find any bugs!

## Installation

    pip install envgen
This will install envgen on your local machine with the following tools:
* **envgen-cli:** Text based interface
* **envgen-api:** API server
* **envgen library:** python 2.7 library for use in other projects

## Text Based Interface
  
1\. Start the Textbased Interface:  
  
    envgen-cli [-h] Project_Directory  
  
The -h option will display the help manual.  
  
Project_Directory should be a directory where you want to store your projects.  Replace the backslashes ('\\') with forward slashes ('/').  
  
Example: "C:/envgen/projects/"  
  
2\. The interface should now be in front of you.  Follow the prompts to build your environments.  Some notes:  
* **Building Entities:** Each entity can have the following attributes:  
    1. **Name:** String with no spaces, slashes, colons, or periods
    2. **OS:** Either 'win' or 'other'
    3. **Provider:** Either 'hyperv' or 'docker'
    4. **Box Name:** Either Vagrant Cloud box_name or Docker Hub image name (not needed if Dockerfile is specified)
    5. **Max Memory:** Only Hyper-V provider, integer in MB
    6. **CPU Cores:** Only Hyper-V provider, integer
    7. **MAC Address:** Only Hyper-V provider, string in format "AA:AA:AA:AA:AA:AA"
    8. **Users:** Only Hyper-V provider, List of tuples consisting of (username,password)
    9. **Software:** Only Hyper-V provider, List of tuples consisting of (Puppet Module, Version)
    10. **Volumes:** Only Docker provider, List of strings in docker volume format "blah:/ef/saf:/foo/bar"
    11. **Ports:** Only Docker provider, List of strings in docker expose ports format "80:80"
    12. **Dockerfile:** Only Docker provider, string containing all of a Dockerfile
* **Check Resources**: Checks required memory is available based on requirements specified or 1GB/entity
* **Building Config**: Saves all configurations to project folder
* **Start Environment**: Starts the environment based on the configs, requires input of hyperv switch during startup
* **Networking**: *Define Hyper-V switches in Hyper-V manager prior to starting the Environment*
  
## API Server

1\. Start the API Server:

    envgen-api [-h] [-p PORT] Project_Directory
    
2\. Now you can access it from http://127.0.0.1:PORT (default port is 2013).

The following resources are available through the API:  
1. Projects (http://127.0.0.1:PORT/projects)
    1. GET - list of projects
    2. POST - project name to create a new project (json: {'project':<name>})
2. Project Operations (http://127.0.0.1:PORT/projects/\<arg\>)
    1. PUT - takes one of the following arguments:
        * 'check' - runs Check Resources and returns errors
        * 'build' - saves configs as defined
        * 'start' - starts environment (This is currently broken as a user needs to define the Hyper-V switch at the command line)
3. Delete Project (http://127.0.0.1:PORT/projects/del/\<name\>)
    1. DELETE - deletes project based on name, returns if successful
4. Host (http://127.0.0.1:PORT/host)
    1. GET - returns hostname, memory utilization, disk space, cpu utilization, and total cpu cores
5. Entities (http://127.0.0.1:PORT/entities)
    1. GET - returns list of entities in the project (see above list in Interface section)
    2. POST - Define a new entity takes all arguments listed above, returns if successful
6. Delete Entity (http://127.0.0.1:PORT/entities/del/\<name\>)
    1. DELETE - deletes an entity based on its name, returns if successful
    
## Library

1\. Import into project:  
  
    from envgen import EnvironmentGenerator
    
2\. Start a new instance:  

    e = EnvironmentGenerator()
    
3\. List of functions available:
  
    updateHost() # Updates all host information listed above returns Host object
    loadEnv(name,directory) # Takes two strings: project name and project directory, 
                            # loads the project from the directory or creates a new project, 
                            # returns if successful
    checkResources() # Checks to make sure enough memory is availabe for the environment
                     # Returns tuple (num_errors, error_string)
    buildEntity(name, box_name, os, provider, max_memory=None, cpu_cores=None, 
                mac=None, users=None, software=None, volumes=None, ports=None, 
                dockerfile=None) # builds an entity based on the inputs (see interface section)
                                 # returns if successful
    removeEntity(name) # removes entity by name, returns if successful
    buildConfig() # builds config files based on project, returns if successful
    startEnv() # starts environment, returns if successful
