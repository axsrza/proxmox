## powerbi web

```bash
https://api.movidesk.com/public/v1/tickets/past?token=36B9762A-5B54-48FF-8E44-F2DA72A8CB8F&
$select=id,type,subject,category,urgency,status,baseStatus,origin,originEmailAccount,owner,ownerTeam,createdBy,serviceFull,serviceFirstLevelId,serviceFirstLevel,contactForm,tags,resolvedIn,reopenedIn,closedIn,lastActionDate,actionCount,lastUpdate,slaAgreement,slaAgreementRule,slaSolutionTime,slaResponseTime,slaSolutionDate,slaResponseDate,slaRealResponseDate,customFieldValues,clients,createdDate&
$filter=createdDate ge 2023-12-01T00:00:00Z and createdDate le 2023-12-31T23:59:59Z and category ne 'Novo ADM' and serviceFirstLevelId ne 561172 and serviceFirstLevelId ne 334031 and subject ne '[Artia] Novo Administrador Cadastrado'&
$orderby=id&
$expand=owner,clients($expand=organization),customFieldValues($expand=items),ownerHistories,statusHistories

```
