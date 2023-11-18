#!/usr/bin/env sh
# Script: md
# Author: kjfreidhof
# Created: November, 14 2023
# About: A shell script that manages docker
# License: MIT

# A varriable for finding the valid option
valid_option_found=false
# For executing bash for the docker container
# The shell that the docker container will run in
bash="/bin/bash"
# Error Messages
error_message="Wrong options here are the valid options"
root="You must be root to run this script"

# A function to pull the image
pull_image() {
        read -rp "Enter docker image you want to pull: " image
        docker pull "$image"




}
# A function to create a container
create_container() {
        read -rp "Create the container you want to create: " container
        read -rp "Enter the image (This is the image you pulled): " image
	docker create --name "$container" -it "$image"
	
	# added the ability to save the name you created into a file. 
	read -rp "Do you want to save the created container: " option 
	if [ "$option" = y ] || [ "$option" = Y ]; then
	       read -rp "Enter the file name you want create: " file_name
       	       echo "$container" >> "$file_name"


	fi 	       
	
}
# A Function to start the container

start_container() {
        read -rp "Enter the container you want to start: " container
        docker start "$container"




}

# A Function  to stop the container
stop_container() {
        read -rp "Enter a container you want to stop: " container
        docker stop "$container"


}

# A function to execute a container
exec_container() {
        read -rp "Enter the container you want to execute: " container
        docker exec -it "$container" "$bash"



}

# A function to see the proccesses with in docker
see_proccess() {
        docker ps

}

# run the docker container once
run_image_as() {
        read -rp "Enter the image you would like to run: " image
        docker run -it "$image"


}




# Help guide for the script
help_guide() {
        echo "-l: This will pull the image specified"
        echo "-i: This will create a container name specified must also specify a image"
        echo "-c: This will start the container based on the container name specified"
        echo "-s: This will stop a container based on the container name specified"
        echo "-t: This will execute the docker container"
        echo "-p: This will let you see the current docker proccesses"
        echo "-r: This will let you run a docker image once."
        echo "-h: Help guide for docker manager script"



}

# An if statment to check if the user is runing the script as root
# if they arent then echo the errot message
# if they are then do nothing move on
if [ "$(id -u)" -ne 0 ]; then
        echo "$root"
        exit 1

fi

# A While loop with command flag options
while getopts "licstprh" options; do
        # case statement of options
        case "${options}"  in
                # if the -l flag is given then pull the image
                l)
                        pull_image
                        valid_option_found=true
                        exit 0
                        ;;

                # if the -i flag is give then create a container

                i)
                        create_container
                        valid_option_found=true
                        exit 0
                        ;;

                # if -c flag is given then start a container
                c)
                        start_container
                        valid_option_found=true
                        exit 0
                        ;;

 # if the -s flag is given then stop the container
                s)
                        stop_container
                        valid_option_found=true
                        exit 0
                        ;;

             # if the -t flag is given then execute a container
                t)
                        exec_container
                        valid_option_found=true
                        exit 0
                        ;;
        # if the -p flag is give then list the proccesses
                p)
                        see_proccess
                        valid_option_found=true
                        exit 0
                        ;;
        # if the -r flag is given then run a image
                r)
                        run_image_as
                        valid_option_found=true
                        exit 0
                        ;;
        # if the -h flag is given then run the help guide
                h)
		        help_guide
                        valid_option_found=true
                        exit 0
                        ;;
        # if wrong option or unknown option run the error message
        # Then run the help guide
                *)
                        echo "$error_message"
                        help_guide
                        valid_option_found=false
                        exit 1
                        ;;



        esac







done

# if there is no option or valid option
# Then show help guide
if ! $valid_option_found; then
        help_guide
        exit 1

fi

# This is needed for the options in the while loop
# Still understanding what it does
shift $((OPTIND-1))

