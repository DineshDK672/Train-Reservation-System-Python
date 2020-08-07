import os
import sqlite3
import time
con = sqlite3.connect("Reservation.db")
crsr = con.cursor()
crsr.execute("CREATE TABLE IF NOT EXISTS Users (Username VARCHAR(255) PRIMARY KEY, Pin INTEGER, Firstname VARCHAR(255), Lastname VARCHAR(255), Email VARCHAR(255), Age INTEGER, Gender VARCHAR(255));")
con.commit()
crsr.execute("CREATE TABLE IF NOT EXISTS Tickets (Ticket_number INTEGER AUTO_INCREMENT, Train_name VARCHAR(255), Firstname VARCHAR(255), Lastname VARCHAR(255), Age INTEGER, Gender VARCHAR(255), FrStn VARCHAR(255), ToStn VARCHAR(255), Seat_number INTEGER, Username VARCHAR(255));")
con.commit()
crsr.execute("CREATE TABLE IF NOT EXISTS Trains (Train_number INTEGER PRIMARY KEY, Train_name VARCHAR(255), Stop_1 VARCHAR(255), Stop_2 VARCHAR(255), Stop_3 VARCHAR(255), Stop_4 VARCHAR(255), Stop_5 VARCHAR(255), Seats VARCHAR(255));") 
con.commit()
# crsr.execute("INSERT INTO Trains VALUES(?,?,?,?,?,?,?,?)",(1234,"Pondichery Express","egmore","tambaram","chengallpatu","villupuram","pondichery","0123456789"))
# con.commit()
# crsr.execute("INSERT INTO Trains VALUES(?,?,?,?,?,?,?,?)",(1256,"Coimbatore Express","central","katpadi","salem","erode","coimbatore","0123456789"))
# con.commit()
# crsr.execute("INSERT INTO Trains VALUES(?,?,?,?,?,?,?,?)",(1678,"Pearl City Express","egmore","villupuram","thiruchirappalli","dindukal","madurai","0123456789"))
# con.commit()
# crsr.execute("INSERT INTO Trains VALUES(?,?,?,?,?,?,?,?)",(1924,"Gummudipoondi Passanger","central","tondiarpet","thiruvottiyur","minjur","gummudipoondi","0123456789"))
# con.commit()
# crsr.execute("INSERT INTO Trains VALUES(?,?,?,?,?,?,?,?)",(8395,"Thiruchendur Express","egmore","villupuram","kumbakonam","madurai","thiruchendur","0123456789"))
# con.commit()
# crsr.execute("INSERT INTO Trains VALUES(?,?,?,?,?,?,?,?)",(3847,"Guruvayur Express","egmore","dindukal","thirunelveli","alappuzha","guruvayur","0123456789"))
# con.commit()
def login():
    try:
        os.system('cls')
        print("\t\t*********************************\n")
        print("\t\t**********  Login page  *********\n\n")
        print("\t\t*********************************\n\n\n\n")
        u_name,pin = input("\nUsername : "),int(input("\nPin : "))
        crsr.execute("SELECT Username , Pin FROM Users;")
        a = crsr.fetchall()
        for row in a:
           if(row[0] == u_name and row[1] == pin):
                 print("\nLogged in Successfully")
                 main(u_name)
                 os.system('cls')
                 return
        print("\nUsername or PIN is incorrect\n")        
        l = int(input("\n1.Sign up\n2.Try again\n\n"))
        if l == 1:
            signin()
        elif l == 2:
            login()
    except Exception as e:
        print(e)
        time.sleep(1.5)
        os.system('cls')
        return

def signin():
    try:
         time.sleep(1.5)
         os.system('cls')
         print("\t\t**********************************\n")
         print("\t\t**********  Signup page  *********\n\n")
         print("\t\t**********************************\n\n\n\n")
         f = True
         u_name = input("\nUsername : ")
         crsr.execute("SELECT Username FROM Users;")
         a = crsr.fetchall()
         for i in a:
            if u_name in i:
                print("Username already taken\n")
                f = False
                l = int(input("1. Login \n2. Try Again\n\n"))
                if l == 1:
                  time.sleep(1.5)
                  login()
                if l == 2:
                  signin()
         if f :       
            pin,f_name,l_name,mailid,age,gender = int(input("\nPIN : ")),input("\nFirstname : "),input("\nLastname : "),input("\nEmail Id : "),int(input("\nAge : ")),input("\nGender : ")
            crsr.execute("INSERT INTO Users VALUES (?,?,?,?,?,?,?)",(u_name,pin,f_name,l_name,mailid,age,gender))
            con.commit()
            login()
    except Exception as e:
         print(e)
         return

def book(u_name):
    try:
         time.sleep(1.5)
         os.system('cls')
         print("\t\t***************************************\n")
         print("\t\t**********  Reservation page  *********\n\n")
         print("\t\t***************************************\n\n\n\n")
         f,t = input("\nFrom : "),input("\nTo : ")
         f,t=(f.lower()).strip(),(t.lower()).strip()
         crsr.execute("SELECT Train_number, Train_name, Seats from Trains WHERE ? IN (Stop_1,Stop_2,Stop_3,Stop_4,Stop_5) AND ? IN (Stop_1,Stop_2,Stop_3,Stop_4,Stop_5) AND Seats!='' ",(f,t))
         a = crsr.fetchall()
         time.sleep(5)
         if not a:
            print("\nCurrently no train is available in that route")
            book()
         while True:
               os.system('cls')
               for rows in a:
                   print("Train No.: "+str(rows[0])+" Train Name : "+rows[1]+" Seats available : "+rows[2]+"\n")
               tn = int(input("\nEnter the Train No. to select : "))
               crsr.execute("SELECT Train_number, Train_name, Seats FROM Trains WHERE Train_number IN (?) AND Seats!='' ",(tn,))
               a = crsr.fetchall()
               time.sleep(2)
               if a:
                  n = int(input("\nEnter the number of Passangers : "))
                  if (n>len(a[0][2])):
                     print("\nSeats are unavailable")
                     time.sleep(2)
                     os.system('cls')
                     continue
                  elif (n>0):                      
                       for i in range(1,n+1):
                           f_name,l_name,age,gender,s_num = input("\nFirst Name : "),input("\nLast Name : "),int(input("\nAge : ")),input("\nGender : "),int(input("\nSeat Num : ")) 
                           while True:
                                 if str(s_num) not in a[0][2]:
                                    print("\nSeat number is not available")
                                    s_num = int(input("\nEnter Seat number : "))
                                 else:
                                     break
                           s_new=a[0][2].replace(str(s_num),'')
                           crsr.execute("UPDATE Trains SET Seats = ? WHERE Train_name IN (?)",(s_new,a[0][1]))  
                           crsr.execute("INSERT INTO Tickets (Train_name, Firstname, Lastname, Age, Gender, FrStn, ToStn, Seat_number, Username) VALUES (?,?,?,?,?,?,?,?,?)",(a[0][1],f_name,l_name,age,gender[0],f,t,s_num,u_name))
                           con.commit()
                       break
    except Exception as e:
          print(e)
          time.sleep(2)
          return 

def check(u_name):
   try:
    time.sleep(1.5)
    os.system('cls')
    print("\t\t**************************************\n")
    print("\t\t**********  My Tickets page  *********\n\n")
    print("\t\t**************************************\n\n\n\n")
    crsr.execute("SELECT Ticket_number, Seat_number, FrStn, ToStn FROM Tickets WHERE Username IN (?) ",(u_name,))
    a=crsr.fetchall()
    print(a)
    time.sleep(5)
    for row in a:
        print("\nTicket No.: "+row[0]+" Seat Number : "+str(row[1])+" From : "+row[2]+" To : "+row[3])
    crsr.execute("SELECT Ticket_number, Train_name, Seat_number, FrStn, ToStn FROM Tickets WHERE Username IN (?) GROUP BY Ticket_number ORDER BY Seat_number",(u_name,))
    a = crsr.fetchall()
    for row in a:
        print("Ticket No.: "+row[0]+" Train Name : "+row[1]+" Seat No.: "+str(row[2])+" From : "+row[3]+" To : "+row[4])
    while True:
      if int(input("\n\nType 1 for menu\n")):
       return
   except Exception as e:
         print(e)
         return

def cancel(u_name):
   try:
    time.sleep(1.5)
    os.system('cls')
    print("\t\t****************************************\n")
    print("\t\t**********  Cancellation page  *********\n\n")
    print("\t\t****************************************\n\n\n\n")
    while True:
          crsr.execute("SELECT DISTINCT Ticket_number, Train_name FROM Tickets WHERE Username IN (?) ",(u_name,))
          b=crsr.fetchall()
          print("\nThe tickets you have booked are : \n") 
          for row in b:
              print(row[0]+" "+row[1]+"\n")
          t = int(input("\nEnter a Ticket number to cancel : "))
          crsr.execute("SELECT DISTINCT Ticket_number FROM Tickets WHERE Ticket_number IN (?) ",(t,))
          a = crsr.fetchall()
          if t in a:
             crsr.execute("SELECT DISTINCT Train_name FROM Tickets WHERE Ticket_number IN (?)",(t,))
             a = fetchall()
             t_name = a[0][0]
             crsr.execute("SELECT Ti.Seat_number , Ti.Train_name, Tr.Seat_number FROM Tickets AS Ti, Trains AS Tr WHERE Ti.Ticket_number IN (?)  AND Tr.Train_name IN (?) GROUP BY Ticket_name",(t,t_name))
             a=fetchall()
             for row in a:
                 print("\nTrain Name : "+row[1]+"\nSeat number : "+str(row[0]))
             s_num = int(input("\nEnter a Seat number to cancel : "))
             s_new = str(s_num).strip()+a[0][2].strip()
             s_new = ''.join(sorted(s_new))
             crsr.execute("DELETE FROM Tickets WHERE Ticket_number IN (?) AND Seat_number IN (?) ",(t,s_num))
             crsr.execute("UPDATE Trains SET Seats = ? WHERE Train_name IN (?)",(s_new,t_name))
             con.commit()
             op=int(input("\n1. Cancel another ticket \n2.Exit\n\n"))
             if op==1:
                continue
             elif op==2:
                return
          else :
               print("\nEnter a valid Ticket Number\n")
               return
   except Exception as e:
         print(e)
         return

def main(u_name):
   try:
     while True:
          time.sleep(1.5)
          os.system('cls')
          print("\t\t******************************************************\n")
          print("\t\t************* Railway Reservation System *************\n")
          print("\t\t******************************************************\n\n\n\n")
          print("Hi "+u_name+" !!\n\n")
          print("1.Book Tickets \n2.My tickets \n3.Cancel tickets \n4.Exit\n\n")
          op=int(input())
          if (op==4):
             os.system('cls')
             return
          elif (op==1):
               book(u_name)
          elif (op==2):
               check(u_name)
          elif (op==3):
               cancel(u_name)
   except Exception as e:
         print(e)
         time.sleep(5)

print("\t\t******************************************************\n")
print("\t\t************* Railway Reservation System *************\n")
print("\t\t******************************************************\n\n\n\n")
print(" Login to continue ")
time.sleep(1.5)
# login()
