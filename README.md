proc import out=project
	datafile="/home/u63582559/BS852/BS852 Mini Project/project_data.csv"
	DBMS=csv replace;
	getnames=yes;
run;
data project_1;
	set project;
	if gait_speed<0.6 then slow_gait_speed=1;
	else if gait_speed>=0.6 then slow_gait_speed=0;
	if alive="Yes" then alivee=1;
	else if alive="No" then alivee=0;
	if sex=2 then sex=1;
	else if sex=1 then sex=0;
run;
proc means data=project_1 descending;
	var age_enrollment age_last_contact grip_strength gait_speed fev1 DSST BMI;
run;
proc freq data=project_1;
	tables sex alive slow_gait_speed smoke high_ed;
run;
/*Q1*/
proc logistic descending;
	model alivee (event="1") = slow_gait_speed;
run;
proc logistic;
	class sex (ref="0") / param=ref descending;
	model alivee (event="1") = slow_gait_speed sex age_enrollment age_last_contact grip_strength fev1 smoke high_ed DSST BMI;
run;
/*Q2*/
proc logistic descending;
	class sex (ref="0") / param=ref;
	model slow_gait_speed = sex age_enrollment age_last_contact grip_strength fev1 smoke high_ed DSST BMI;
run;
/*Q3*/
proc logistic descending;
	class sex (ref='0') / param=ref;
	model alivee (event='1') = age_enrollment age_last_contact grip_strength fev1 smoke high_ed DSST BMI slow_gait_speed | sex;
	estimate "Male" slow_gait_speed 1 / exp cl;
	estimate "Female" slow_gait_speed 1 slow_gait_speed*sex 1/ exp cl;
run;
/*Q4*/
proc logistic descending;
	class sex (ref="0") / param=ref;
	model alivee (event="1") = sex age_enrollment age_last_contact grip_strength fev1 smoke high_ed DSST BMI / selection=stepwise sle=0.15;
run;
