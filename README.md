# Project3
from urllib import request
from urllib.request import urlretrieve
urlPath = 'https://s3.amazonaws.com/tcmg476/http_access_log'
localcopy = 'accesslog.log'
import requests
url = 'https://s3.amazonaws.com/tcmg476/http_access_log'
r = requests.get('https://s3.amazonaws.com/tcmg476/http_access_log', stream = True)
with open('python.txt','wb') as textfile:
  for chunk in r.iter_content(chunk_size=1024):
    if chunk:
      textfile.write(chunk)
result ={
'total_requests':0,
'per_day_data':{},
'per_week_data':{},
'per_month_data':{},
'request_not_successful':0,
'requests_redirected_elsewhere':0,
'filewise_request_frequency':{},
'most_requested_file':[0,[]],
'least_requested_file':[0,[]]
}
#Got to this point and was given error. Could not get beyond this point. Worked with another group and helped me understand everything #that followed but could not figure out why I recieved a python.txt dosnt exist. 
file = open('python.txt')
date_day = None
days = 0
week = None
months_done = []
for line in file:
  if(len(line)>=56):
    result['total_requests']+=1
    data=line.split()
    date = data[3][1::].split(‘:’)
    if not (date_day == date[0]):
      date_day = date[0]
      days += 1
      if(days%7 == 0):
        week = date_day
    if date[0] in result['per_day_data']:
      result['per_day_data'][date[0]]+=1
    else:
      result['per_day_data'][date[0]]=0
    if week in result['per_week_data']:
      result['per_week_data'][week]+=1
    else:
      result['per_week_data'][week] = 0
    month = date[0][3::]
      if month not in months_done:
        file_name = month[:3:]+month[4::]
        if(len(file_name)) == 7:
          month_file = open(month[:3:]+month[4::]+'.txt',‘w’)
          print(file_name)
        months_done.append(month)
      month_file.write(line)
      if month in result['per_month_data']:
        result['per_month_data'][month]+=1
     else:
        result['per_month_data'][month]=0
     if data[-2][0]==“4”:
        result[“request_not_successful”]+=1
     if data[-2][0]==“3”:
        result[“requests_redirected_elsewhere”]+=1
     if data[6] in result[“filewise_request_frequency”]:
        result[“filewise_request_frequency”][data[6]]+=1
     else:
        result[“filewise_request_frequency”][data[6]]=1
max=result[“filewise_request_frequency”][“index.html”]
min=result[“filewise_request_frequency”][“index.html”]
maxlist=[“index.html”]
minlist=[“index.html”]
for i in result[“filewise_request_frequency”]:
  if result[“filewise_request_frequency”][i] > max:
     max = result[“filewise_request_frequency”][i]
     maxlist=[i]
  if result[“filewise_request_frequency”][i] < min:
     min = result[“filewise_request_frequency”][i]
     minlist=[i]
  if result[“filewise_request_frequency”][i] == max:
     maxlist.append(i)
  if result[“filewise_request_frequency”][i] == min:
     minlist.append(i)
result[“most_requested_file”]=[max,maxlist]
result[“least_requested_file”]=[min,minlist]
print(result)
