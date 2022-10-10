# PowerApps-Project-1

This is a two-screen app which allows the user (nurses) to submit patient's wellness evaluation in the system. The app uses a SQL Server connection to fetch and update patient's data.

## Steps involved in creating the app:

**1) Creating a connection to SQL Server:**

```
Steps:
 Go to PowerApps Homepage
 On the navigation pane, select Dataverse > Connections > New Connection
 Select SQL Server > Enter the credentials as show below
```

![PowerApps - Data Connection](https://user-images.githubusercontent.com/111697358/194720506-50c2e0b4-0d04-4c4e-a852-a90c0e96ad97.PNG)

**2) Adding NusingPatient & NursingWellCheck tables from the SQL server**

![PowerApps - Add Data](https://user-images.githubusercontent.com/111697358/194720530-0ee14911-e44c-4edc-a543-7ed116c6641d.PNG)

**3) Initialising variables inside OnStart property of the app**

```
Set(varBackgroundGreen, RGBA(232, 244, 220, 1));
Set(varHeaderGreen, RGBA(53, 91, 14, 1));
Set(varCoreGreen, RGBA(78, 132, 22, 1));
Set(varUserEmail, User().Email);
```

**4) Application Main Screen (*scrMain*)**

![PowerApps Homepage](https://user-images.githubusercontent.com/111697358/194857534-f3c9b8b1-b0d6-473f-b287-967d4dd3ba39.png)

- Homepage Header

```
Property:
 Text = "Wellness Check Application"
 Fill = varHeaderGreen
```

- Search Bar for Patient Name

```
Property:
 Name = inpPatientName
```

- Vertical Gallery displaying list of Patients

```
Property:
 Name = galPatientName
 Items = Search(NursingPatient, inpPatientName.Text, "FirstName", "LastName")
 Display records within Gallery using Title = ThisItem.FirstName & " " & ThisItem.LastName
 Fill = varCoreGreen
 Transition = Pop
 ```

- Patient Info Details Section

```
Property:
 Header Text = "Patient Info for " & galPatientName.Selected.FirstName & " " & galPatientName.Selected.LastName
 Image = galPatientName.Selected.PatientPhoto
 Phone Number = galPatientName.Selected.PhoneNumber
 Age = "Age : " & galPatientName.Selected.Age
 Gender = "Gender : " & galPatientName.Selected.Gender
 Depression Check Icon = If(galPatientName.Selected.CheckDepressionLevels, Icon.Check, Icon.Cancel)
 Diabetes Check Icon = If(galPatientName.Selected.CheckDiabetesLevels, Icon.Check, Icon.Cancel)
 New Application Button = Navigate(scrNewAssessment,ScreenTransition.Cover); NewForm(Form1)
``` 

- Assessment Details Section

> Created headers for each value to display under the gallery and stacked them side to side

```
Property:
 Gallery Items = Sort(Filter(NursingWellCheck, PatientID = galPatientName.Selected.PatientID), WellCheckDate, Descending)
 Assesment Date = ThisItem.WellCheckDate
 Blood Pressure = ThisItem.BPDiastolic & " / " & ThisItem.BPSystolic
 OverallRating = ThisItem.OverallRating
 Comments = ThisItem.Comments
 Edit Icon = Navigate(scrNewAssessment,ScreenTransition.Cover); EditForm(Form1)
 Delete Icon = UpdateContext({varShowDeleteScreen: true})
```

9) Delete Record Section

> Label asking user to confirm the delete action

```
Yes = Remove(NursingWellCheck,galWellnessCheck.Selected);UpdateContext({varShowDeleteScreen: false });Notify("The assessment was successfully deleted!!",NotificationType.Success,2000)
No = On Select UpdateContext({varShowDeleteScreen: false})
```

==New Assessment / Edit Records Screen==

Header = "Wellness Check Assessment for " & galPatientName.Selected.FirstName & " " & galPatientName.Selected.LastName
Assessor = If(Form1.Mode = FormMode.New, User().Email, ThisItem.Assessor)
BPDiastolic = 
BPSystolic = 
Comments = Multiline
Depression Rating = Default 3
GlucoseLevel = 
OverallHealthRating = Default 3
PatientID = Selected item from galleries
WellCheckDate = Now()
Submit = SubmitForm(Form1); Notify("The assessment was saved successfully!!", NotificationType.Success, 2000); NewForm(Form1)
Cancel = Navigate(scrMain, ScreenTransition.Fade); NewForm(Form1)

On selection of an edit against a record under wellnesscheck gallery, the fields under NewAssessment are auto-populated with the record details.

And the user can make changes and then re-submit or go back to the previous screen.
