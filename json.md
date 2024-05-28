## powerbi web

```bash
https://api.movidesk.com/public/v1/tickets/
#token
$select=id,protocol,type,subject,category,urgency,status,baseStatus,justification,origin,originEmailAccount,owner,ownerTeam,createdBy,serviceFull,serviceFirstLevelId,serviceFirstLevel,contactForm,tags,cc,resolvedIn,reopenedIn,closedIn,lastActionDate,actionCount,lastUpdate,lifetimeWorkingTime,stoppedTime,stoppedTimeWorkingTime,resolvedInFirstCall,sequence,slaAgreement,slaAgreementRule,slaSolutionTime,slaResponseTime,slaSolutionChangedByUser,slaSolutionDate,slaSolutionDateIsPaused,slaResponseDate,slaRealResponseDate,customFieldValues,clients,createdDate&
$filter=createdDate ge 2024-04-01T00:00:00Z and createdDate le 2024-04-30T23:59:59Z and category ne 'Novo ADM' and serviceFirstLevel ne '[ANTIGO] Retenção e Expansão'&
$orderby=id&
$expand=owner,actions($select=origin,id),actions($expand=timeAppointments($expand=createdBy)),clients($expand=organization),customFieldValues($expand=items)

```