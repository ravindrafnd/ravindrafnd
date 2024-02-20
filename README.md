BEGIN
Declare partitiongrp varchar(5000);
Declare stmt varchar(5000);
set partitiongrp= (select concat('p', group_concat(lead_id SEPARATOR ',p'),'') from voicemessaging.lead_info info 
 where info.lead_user_id=116 and info.created_date >= DATE_SUB(NOW(),INTERVAL 24 HOUR));

set @stmt=concat("select   PHONE_NUMBER,'niromoney' as username,116 as userid,info.lead_name ,(select campaign_name from campaign where CAMPAIGN_ID=info.LEAD_CAMPAIGN_ID)leadName ,0 attemptNum,lid.CREATED_DATE startDate,'' answerDate,\t'' endDate,'' callDuration,\t0 billSeconds,\t0 creditsUsed,\t0 disposition,0 clid,0 hangupcode,'' digitpresse\nfrom lead_info info,lead_info_detail   partition(",partitiongrp,")  lid where lid.created_date >= DATE_SUB(NOW(),INTERVAL 24 HOUR) and \n  info.lead_user_id=116 and lid.lead_id=info.lead_id and  PHONE_NUMBER_STATUS<>'OK'");
PREPARE stmt2 FROM @stmt;
    EXECUTE stmt2;
   
    DEALLOCATE PREPARE stmt2 ;                   
END
