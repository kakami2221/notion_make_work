# notion_make_work
from notion_client import Client
import os
from datetime import datetime



token = os.getenv("NOTION_TOKEN")
notion = Client(auth=token)

dateID = "..."
n = 0
while n == 0:
    try:
        num = int(input('課題の個数：'))
        break
    except:
        print('誤入力')
        continue

mylist = []
for i in range(num):
    while n == 0:
        try:
            kname = input('課題の名前：')
            timed = list(map(int,input('課題の締め切り：').split()))
            mylist.append([kname,timed])
            break
        except:
            print('誤入力')
            continue

nowtime = datetime.now().isoformat()

prpt = {}
db = notion.databases.retrieve(database_id=dateID)
print("=== DB のプロパティ一覧 ===")
for name, info in db["properties"].items():
    print(f"{name!r} → {info['type']}")
    prpt[info['type']] = name

userID = {}
users = notion.users.list()
for u in users["results"]:
    userID[u.get("name")] = u['id']

for method in mylist:
        try:
            endtime = datetime(method[1][0],method[1][1],method[1][2],23,59)
            notion.pages.create(
                parent = {"database_id":dateID},
                properties = {
                    prpt['title']:{
                        "title":[{"text": {"content":kname}}]
                    },
                    prpt['date']:{
                        "date":{
                            "start":nowtime,
                            "end":endtime.isoformat()
                        }
                    },
                    prpt['status']:{
                        'status':{
                            'name':'未着手'
                        }
                    },
                    prpt['people']:{
                        'people':[
                            {'object': "user", "id":userID['kakami'] }
                        ] 
                    }
                }
            )
        except:
            print('error')
print('完了')    
