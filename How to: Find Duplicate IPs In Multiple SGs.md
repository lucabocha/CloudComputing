import re

my_allowed_ips = []

with open("DMZ_SGs_Outputs", "r") as thefile:
	for line in thefile:
		if re.search(".*\..*\..*\..*", line) and not re.search("0\.0\.0\.0", line):
			splitted_line = line.split()
			my_allowed_ips.append(splitted_line[1])

for ip in my_allowed_ips:
	if my_allowed_ips.count(ip) > 1:
		print("The following IP address is repeated: " + ip + " on the SGs " + str(my_allowed_ips.count(ip) - 1 ) + " times")


aws ec2 describe-security-groups --group-ids <Security \Group ID> --output table >> DMZ_SGs_Outputs 
echo " " >> DMZ_SGs_Outputs 
echo " " >> DMZ_SGs_Outputs 
