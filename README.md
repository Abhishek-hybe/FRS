INSTALLING REQUIRED PACKAGES
pip install opencv-python
pip install numpy
pip install face_recognition   # FRS

Let’s import our required libraries first

import face_recognition
import cv2
import numpy as np
import csv
import os
from datetime import datetime
face_recognition is an ai model that scans and recognize human faces, cv2 is opencv-python package, csv will be used for manipulating data in csv file, os to handle files and folders

video_capture = cv2.VideoCapture(0)
Videocapture is a method of opencv that takes input (here source is 0 or default webcam)

jobs_image = face_recognition.load_image_file("photos/jobs.jpg")
jobs_encoding = face_recognition.face_encodings(jobs_image)[0]

ratan_tata_image = face_recognition.load_image_file("photos/tata.jpg")
ratan_tata_encoding = face_recognition.face_encodings(ratan_tata_image)[0]

sadmona_image = face_recognition.load_image_file("photos/sadmona.jpg")
sadmona_encoding = face_recognition.face_encodings(sadmona_image)[0]

tesla_image = face_recognition.load_image_file("photos/tesla.jpg")
tesla_encoding = face_recognition.face_encodings(tesla_image)[0]

known_face_encoding = [
jobs_encoding,
ratan_tata_encoding,
sadmona_encoding,
tesla_encoding
]

known_faces_names = [
"jobs",
"ratan tata",
"sadmona",
"tesla"
]
load_image_file is used to load images face_encodings will create encoded data for that image that face_recognition package will use for performing operations, we have 4 faces in our example to recognize, known_face_encoding is the list of encoding of all the 4 faces , known_faces_names is name of all of them


students = known_faces_names.copy()

face_locations = []
face_encodings = []
face_names = []
s=True

now = datetime.now()
current_date = now.strftime("%Y-%m-%d")

f = open(current_date+'.csv','w+',newline = '')
lnwriter = csv.writer(f)
students is a copy of know faces that we will use mark the attendance (basically we will remove names that are present), face_locations, face_encodings, face_names are empty lists for input image (we will compare with specific recognized face for recognition), current time is current time (as the name suggests) we will use this for accurate attendance, f is the variable having all the data of current date csv file and we are opening it with write mode, lnwriter is write variable on f

while True:
    _,frame = video_capture.read()
    small_frame = cv2.resize(frame,(0,0),fx=0.25,fy=0.25)
    rgb_small_frame = small_frame[:,:,::-1]
    if s:
        face_locations = face_recognition.face_locations(rgb_small_frame)
        face_encodings = face_recognition.face_encodings (rgb_small_frame, face_locations)
        face_names = []
we will create a infinite loop and store the incoming frame into frame variable , a new small frame variable is created to store resized image and the scale of decrement is 0.25% on both x and y rgb_small_variable will store the rgb equivalent of the small frame, we need this as face_recognition package used rgb format ,face_locations and face_encodings variables will store the face encoding and locations of incoming frames

for face_encoding in face_encodings:
            matches = face_recognition.compare_faces(known_face_encoding,face_encoding)
            name=""
            face_distance = face_recognition.face_distance(known_face_encoding,face_encoding)
            best_match_index = np.argmin(face_distance)
            if matches[best_match_index]:
                name = known_faces_names[best_match_index]

            face_names.append(name)
            if name in known_faces_names:
                font = cv2.FONT_HERSHEY_SIMPLEX
                bottomLeftCornerOfText = (10,100)
                fontScale              = 1.5
                fontColor              = (255,0,0)
                thickness              = 3
                lineType               = 2

                cv2.putText(frame,name+' Present', 
                    bottomLeftCornerOfText, 
                    font, 
                    fontScale,
                    fontColor,
                    thickness,
                    lineType)
we will create a for loop to iterate on face_encoding values and inside for looop we will compare incoming encoding and locations with know ones and if its present we will recognise what is the name of that face (for more detailed explanation watch the video) after that we will append that name into face_names list and display a text on the video output with name , font is saved in fonts variable , and opencv will use putText to actually put that text

 if name in students:
                    students.remove(name)
                    print(students)
                    current_time = now.strftime("%H-%M-%S")
                    lnwriter.writerow([name,current_time])
    cv2.imshow("attendence system",frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

video_capture.release()
cv2.destroyAllWindows()
f.close()
if the name is present in students list remove it from there as the student is marked as present once , current time is updated in the csv using date time package , the final task is to display the user video stream and also a exit condition which in this case is press of button ‘q’, after this the only thing left is to release the video capture (close video input stream) destroy all opened windows and close the opened file
