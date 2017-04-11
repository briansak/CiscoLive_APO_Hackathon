# IoT Device Tracking

import requests
import smtplib
import threading
import time
import math

# Globals
x = 0
email_sent = 0
recovery_sent = 0
requests.packages.urllib3.disable_warnings()

# AP Locations

AP = {1:[15.02,31.77,"Client_1"],
	2:[26.86,121.08,"AP_North"],
	3:[56.46,108.66,"Client_2"],
	4:[33.46,10.35,"AP_South"],
	5:[14.8,84.11,"AP_East"],
	6:[45.9,56.64,"AP_West"]}

def f():

	global AP
	global x
	global email_sent
	global recovery_sent

	# Setup API Calls

	api_url = "https://10.1.1.21/api/contextaware/v1/location/clients/00:0f:60:02:e3:c4"
	api_url2 = "https://10.1.1.21/api/contextaware/v1/location/clients/00:0f:60:03:19:d4"
	headers = {'accept': 'application/json'}

	resp = requests.get(api_url, auth=('hackathon', 'hackathon'), verify=False, headers=headers)
	data = resp.json()

	resp2 = requests.get(api_url2, auth=('hackathon', 'hackathon'), verify=False, headers=headers)
	data2 = resp2.json()

	# Calculate Device Location and Association Status

	associated = data['WirelessClientLocation']['dot11Status']
	associated2 = data2['WirelessClientLocation']['dot11Status']
	coordinates = data['WirelessClientLocation']['MapCoordinate']
	x_coordinate = coordinates['x']
	y_coordinate = coordinates['y']

	
	# Calculate Closest AP

	shortest = 200

	for key in AP:
		dist = math.sqrt( (x_coordinate - AP[key][0])**2 + (y_coordinate - AP[key][1])**2)
		if dist < shortest:
			shortest = dist
			shortest_ap = AP[key][2]

	# Setup SMS Communicaiton through Gmail

	fromaddr = 'IoT_Tracking@company.com'
	toaddr  = '[SMS Number]@txt.att.net'
	gmail_username = 'gmail_user'
	gmail_password = 'gmail_pass'

	msg = "\r\n".join([
  "From: hackathon@gmail.com",
  "To: [SMS Number]@txt.att.net",
  "Subject: Device Disconnected!",
  "",
  "Last seen near AP " + shortest_ap + " Coordinates: " + str(x_coordinate) + ", " + str(y_coordinate)
  ])

 	msg2 = "\r\n".join([
 	"From: hackathon@gmail.com",
 	"To: [SMS Number]@txt.att.net",
 	"Subject: Repaired!",
 	"",
 	"Device e3:c4 replaced with 19.d4"
	])

	x = x + 1
	print x, associated, associated2
	print x_coordinate,y_coordinate,shortest_ap,shortest

	if (associated == "PROBING" and email_sent == 0):

		# If device goes missing, send SMS

		server = smtplib.SMTP('smtp.gmail.com:587')
		server.ehlo()
		server.starttls()
		server.ehlo()
		server.login(gmail_username,gmail_password)
		server.sendmail(fromaddr, toaddr, msg)
		server.close()
		email_sent = 1

	if (associated2 == "ASSOCIATED" and recovery_sent == 0):

		# If device goes missing, send SMS

		server = smtplib.SMTP('smtp.gmail.com:587')
		server.ehlo()
		server.starttls()
		server.ehlo()
		server.login(gmail_username,gmail_password)
		server.sendmail(fromaddr, toaddr, msg2)
		server.close()
		recovery_sent = 1

	threading.Timer(10, f).start()

f()
