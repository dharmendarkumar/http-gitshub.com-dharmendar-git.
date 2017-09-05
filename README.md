# http-gitshub.com-dharmendar-git.
dharmendar kumar 
https://github.com/dharmendarkumar/http-gitshub.com-dharmendar-git..git
libname sas1 '/folders/myshortcuts/SASUniversityEdition/myfolders/sas1';

proc import datafile='/folders/myshortcuts/SASUniversityEdition/myfolders/sasclass/OLYMPICS.xls'
out= sas1.olympics dbms= xls replace; getnames= yes; run;  

proc sql outobs=6;
create table summary_report as
select distinct Probability____ as Probability, count(Probability____) as Nbr_of_Optys,
 sum(Total_Media_Value) as Tot_Budget, (sum(Total_Media_Value))* Probability____ as Tot_forecast
 from sas1.olympics 
 group by Probability____
 order by Probability____ desc;
 quit;
 
 
 proc report data = summary_report ;
 title 'London Olympic Pipeline As of 17th November 2012 1';
 columns Probability Nbr_of_Optys Tot_Budget  Tot_forecast;
 define Probability/ group width = 12 style(header)= [backgroundcolor=bgr];
 define Nbr_of_Optys/ group width= 13 sum format = dollar15. style(header)= [backgroundcolor=bgr];
 define Tot_Budget/group width =11 sum format= dollar15. style(header)= [backgroundcolor=bgr];
 define Tot_forecast/group width= 13 sum  format = dollar15. style(header)= [backgroundcolor=bgr];
 rbreak after /skip summarize style= [backgroundcolor=bgr];
 run;
 
 
 proc import datafile= '/folders/myshortcuts/SASUniversityEdition/myfolders/sasclass/OLYMPICS_DIGI.xls'
 out= sas1.olympic_digi dbms= xls replace; getnames= yes;
 run;
 
 
 proc sql outobs=6;
create table summary_report1 as
select distinct _probability___ as Prob_Digi, count(_probability___) as Nbr_of_Optys,
 sum(Total_Media_Value) as Digital_Bugt, (sum(Total_Media_Value))* _probability___ as D_forecast
 from sas1.olympic_digi 
 group by _probability___
 order by _probability___ desc;
 quit;
 
  proc report data = summary_report1 ;
 title 'London Olympic Pipeline As of 17th November 2012 1';
 columns Prob_Digi  Nbr_of_Optys Digital_Bugt  D_forecast;
 define  Prob_Digi/ group width = 10 style(header)= [backgroundcolor=bgr];
 define Nbr_of_Optys/ group width= 13 sum format = dollar15. style(header)= [backgroundcolor=bgr];
 define Digital_Bugt/group width =13 sum format= dollar15. style(header)= [backgroundcolor=bgr];
 define D_forecast/group width= 11 sum  format = dollar15. style(header)= [backgroundcolor=bgr];
 rbreak after /skip summarize style= [backgroundcolor=bgr];
 run;
 
 /****************************************************************/
 data olympics_xls ;
 set sas1.olympics;
 label
Probability____ = 'Probability',
Account_Name='Client',
Opportunity_Owner='Champ',
Total_Media_Value='Total_Budget',
Deal_Comments='Deal_Comments',
Last_Modified_Date='Modified';
run;

 data olympics_digi ;
 set sas1.olympics_digi;
 rename
Total_Media_Value =Value;
label
Total_Media_Value ='Digital_Bugt';
run;
 
 proc sort data= olympics_xls out= new1 ;
 by Account_Name;
 run;
 
 Proc sort data= olympics_digi out= new2;
 by Account_Name;
 run;
 
 data new_olympics;
 merge new1  new2;
 by Account_Name;
 run;
 
proc sql ;
create table new_final as
select distinct Probability____ as Probability,
Account_Name as Client,
Opportunity_Owner as Champ,
 Last_Modified_Date as Modified,
 Total_Media_Value as Total_Budget,
  Value as Digital_Budget,
 Deal_Comments as Deal_Comments
  from new_olympics
 group by Probability____
 order by Probability____ desc ;
 quit;
 
 proc print data =new_final;
 run;
 
 proc report data= new_final missing ;
title 'Olympic Pipeline (LONDON) - by Probability As of 17th November 2012';
column Probability Client Champ Modified Total_Budget Digital_Budget Deal_Comments ;
define Probability/ group width=11 descending style (header)=[backgroundcolor=bgr];
define Client/ group width=12 style (header)=[backgroundcolor=bgr];
define Champ/ group width=12  style (header)=[backgroundcolor=bgr];
define Modified/ group width=12  style (header)=[backgroundcolor=bgr];
define Total_Budget/ group width=12 sum  format= dollar15. style (header)=[backgroundcolor=bgr];
define Digital_Budget/ group width=12 sum  format= dollar15. style (header)=[backgroundcolor=bgr];
define Deal_Comments/ group width=12 style (header)=[backgroundcolor=bgr];
break after  Probability/  skip summarize style= [backgroundcolor=bgr] ;
rbreak after / skip summarize style=[backgroundcolor=bgr];
run;

/*******************************************/

data sas1.olympics_;
 set sas1.olympics;
 label
Account_Name='Client',
Opportunity_Owner='Champ',
Probability____ = 'Probability',
Last_Modified_Date='Modified',
Total_Media_Value='Total_Budget',
Deal_Comments='Deal_Comments';
run;
 
 proc sort data = sas1.olympics_;
 by Account_Name;
 run;

proc sql;
create table final_summary as
select
Account_Name as Client,
Opportunity_Owner as Champ,
 Probability____ as Probability,
 Last_Modified_Date as Modified,
 Total_Media_Value as Total_Budget,
Deal_Comments as Deal_Comments
from sas1.olympics_
where Probability>0
group by client
order by Client;
quit;


proc report data = final_summary;
title 'London Olympic Pipeline - Client As of 17th November 2012';
columns Client Champ Probability Modified Total_Budget Deal_Comments;
define Client/  style(header)=[backgroundcolor=bgr];
define Champ/ style(header)=[backgroundcolor=bgr];
define Probability/   style(header)=[backgroundcolor=bgr];
define Modified / style(header)=[backgroundcolor=bgr];
define Total_Budget/  format = dollar15. style(header)=[backgroundcolor=bgr];
define Deal_Comments/ style(header)=[backgroundcolor=bgr];
run;
 
 /************************************************************************************/


proc sql;
create table practice as
select distinct * 
from sas1.olympics
;
 quit;
 
 proc sql;
 create table practice_1 as
 select distinct * from sas1.olympics_digi
 ;
 quit;

proc sql;
create table merge_1 as
select distinct a.*, b.*  from practice as a left join practice_1 as b  on a.Account_Name = b.Account_Name;
quit;


proc sql;
create table merge_1 as
select distinct a.Probability____ as Probability,a.Account_Name as Client,
 a.Opportunity_Owner as Champ, a.Total_Media_Value as Total_Budget, 
 a. Deal_Comments as Deal_Comments, b.Total_Media_Value as Digital_Bugt,
 b.Last_Modified_Date as Modified 
 from practice as a left join practice_1 as b  on a.Account_Name = b.Account_Name
 group by Probability____
 order by Probability____ desc ;
quit;


ods pdf file ='/folders/myshortcuts/SASUniversityEdition/myfolders/sasuser.v94/merge_1.pdf ';
 proc report data= merge_1 missing;
title 'Olympic Pipeline (LONDON) - by Probability As of 17th November 2012';
column Probability Client Champ Total_Budget Deal_Comments Modified Digital_Bugt;
define Probability/ group width=11 descending style (header)=[backgroundcolor=bgr];
define Client/ group width=12 style (header)=[backgroundcolor=bgr];
define Champ/ group width=12  style (header)=[backgroundcolor=bgr];
define Total_Budget/ group width=12 sum  format= dollar15. style (header)=[backgroundcolor=bgr];
define Deal_Comments/ group width=12 style (header)=[backgroundcolor=bgr];
define Modified/ group width=12  style (header)=[backgroundcolor=bgr];
define Digital_Bugt/ group width=12 sum  format= dollar15. style (header)=[backgroundcolor=bgr];
break after  Probability/  skip summarize style= [backgroundcolor=bgr] ;
rbreak after / skip summarize style=[backgroundcolor=bgr];
run;
ods pdf close;











 
 
 
 
 
