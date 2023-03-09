# What's the motivation? Azure Cloud Service, Azure Cloud Service Extended to Azure App Service.

I often came into the conversation recently with different people around Azure Cloud Service - Extended Vs Azure App Service. Simple Questions - 

	1. Why should we be thinking of moving to App service? We are happy with Azure Cloud Service - Extended that fits our needs.
	2. Pricing calculator shows cost of App service is more than the cost of Azure Cloud Service what we are paying right now? Why the prices are higher?
	3. What is Microsoft's roadmap on Azure Cloud Service- Extended?  etc. etc.…

So here it is. First things first, let's go back to the history here. 


```
Please note : This article is not intended to highlight differences across App services, Azure Cloud Service Classic and Extended 
rather to help you how you should be approaching when making decisions on migrations by looking 
at different aspects  like pricing, SLA, feature set etc.

```

Azure Cloud Service was one of the first Platform-as-a-Service on Azure over Service Management Platform in the year 2012.It was primarily designed to support applications that are scalable, reliable and inexpensive to operate. This technology was running on Virtual Machines under the hood. But It has greater control, as you can install your own software's or any third party libraries dependency along with accessing them remotely. But this is now deprecated for new customers and for all customers, it will completely get retired on Aug 31st, 2024. Refer link [Cloud Services (classic) deployment model is retiring on 31 August 2024 | Azure updates | Microsoft Azure](https://azure.microsoft.com/en-us/updates/cloud-services-retirement-announcement/).

I personally have seen both sides of the platform, did executed couple of migration projects, with evolution of ARM and enhancements coming on board on the ARM side, really pushed the conversation for migrating from Classic to ARM or onboard directly on ARM for new workloads. ARM has a lot of great features - Role Based Access Control, Resource Provider Model etc..

However, With the rise of Azure Cloud Services in Windows Azure Platform aka Azure, many organizations started their cloud journey, evaluate the cloud platform and become the first organization thinking / moving to cloud. There were many projects executed, 4 of which I had the pleasure of executing in 2012 over this fantastic technology  Azure Cloud Service - Web Roles / Worker Roles. As we speak today, these workloads are still running as production workloads in many organizations.

As years passed, ARM took the major share of popularity in Azure than Service Management and why not, because Microsoft's Azure Platform roadmap was in the direction of ARM. But Service Management is still a day2day platform for certain organizations and workloads.
Recently Microsoft introduced Azure Service Management - Extended Support not only to extend the support for Azure Cloud Services for existing customer ensuring feature parity with Classic one but also bringing in goodies from ARM like Role Based Access Control, ARM template based Deployment, homogeneous DevOps IaC practice - using ARM templates across an org etc.. More Info on Azure Cloud Service Extended will be found here [Azure Cloud Services (extended support) documentation | Microsoft Learn](https://learn.microsoft.com/en-us/azure/cloud-services-extended-support/).

Along with this we also have another capability, Azure App Service evolving at full throttle since  so many years. Lots of workloads running over App services, supports lots of deployment patterns and network designs. This has also become a popular platform of building Cloud Native Applications or hosting Applications. It can also support running containerized workloads.

But now the questions is "What's my motivation to move to Azure App services?"

At the time of writing this, Customers who wants more control over VM's instances powering Cloud Services may choose Azure Cloud Services(Extended) over Azure App services. Example Scenarios like registering COMs, Setting Registry Keys, install libraries or dependencies of your application etc. For more information refer configure startup tasks [Run Startup Tasks in Azure Cloud Services (classic) | Microsoft Learn](https://learn.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks). 

Then the question pops Will Azure Cloud Service eventually go away? May be but not near future, In fact right now it exists for a reason. App Service scale unit is still deployed over Cloud  Service as well as VM Scale sets. And also looking at the vast  footprint of App services being deployed & maintained across so many years now, Azure Cloud Service will still have a decent amount of share in App services as well. So, at this point If organizations  still wants to continue using Azure Cloud Service (Extended), they can still continue with same for few upcoming years at least. This does not mean that Azure Cloud Service(Extended) should be considered for any new workloads over App service migration. Technically one can argue why not, but my opinion is that you might not want to double the effort of your cloud journey for each single workload instead a little extra can get you future proof  as well as open access to more enhanced capabilities and features. For new workloads or even existing where rearchitecting is considered, preference will be App services instead of Azure Cloud Service (Extended).

Let's have a look at different wavelengths on this. To understand it better let's assume a scenario, 

*Suppose there is a fictious retail company , Contoso IT department hosted their consumer facing application in Azure over Cloud Service (Classic) -Web Roles and Worker Roles. With  Microsoft's announcement of End of Life of Azure Cloud Service - Classic, migrating the application became an important priority. Contoso IT team started evaluating different migration options and finalized on 2 possible migration paths. Azure Cloud Service - Extended and Azure App Service. Evaluation happened on multiple dimensions, some of them are below.*

**Roadmap** : 

Azure Cloud Services (extended support) will primarily maintain feature parity with Cloud Services (classic.) whereas Azure App Service will remain the citizen product and will continue to feature new capabilities.

**Service Level Agreement** : 

SLA for Azure cloud services are eligible only when you deploy 2  or more role instances. This is not the case with app services.

Direct from  [SLA for App Service | Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/sla/app-service/v1_5/)
*"We guarantee that Apps running in a customer subscription will be available 99.95% of the time. No SLA is provided for Apps under either the Free or Shared tiers."*

Direct from [SLA for Azure Cloud Services | Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/sla/cloud-services/v1_5/)
*"For Cloud Services, we guarantee that when you deploy two or more role instances in different Update Domains, at least one role instance will have Role Instance Connectivity at least 99.95% of the time."*


**Scaling across availability zones and regions**

Cloud Services cannot scale across availability zones and regions.  

**Managed Identity support**

Azure Cloud Service does not support managed identities whereas app services do. To learn more about managed identities refer [Managed identities for Azure resources - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview) and how to use it with  app services [Managed identities - Azure App Service | Microsoft Learn](https://learn.microsoft.com/en-us/azure/app-service/overview-managed-identity?tabs=portal%2Chttp)

**Azure Pricing** :

Using pricing calculator and simply comparing both of these on cores/memory/storage is not the right way of evaluating price difference, I mean it will lead you to right figures.  Let me explain why. The retail application has multiple environments - prod, QA, TEST, Dev etc. If you simply go to pricing calculator and check the pricing based on cores/Memory, honestly It's not the right way.. Yes, You first need to map your Cloud Service Instance with  App service Plan capacity, but then there are few other things you need to do. Those cores/memory are available across multiple plans in App services Basic, standard, premiumV2, premium V3, isolated, isolated V2 etc.. Classified across shared and dedicated compute models. For example you need to understand what's the baseline performance you need and what's your benchmark performance recorded when scaling out across peak demands etc. which will help you select the right plans. Memory/Core/Storage combinations are blended via plans in App services, also these plans differ in the capabilities they offer like Scale-out units etc. For example The new PremiumV3 pricing tier guarantees machines with faster processors (minimum 195 ACU per virtual CPU), SSD storage, and quadruple memory-to-core ratio.  Check here for more details around plans and pricing [App Service Pricing | Microsoft Azure](https://azure.microsoft.com/en-us/pricing/details/app-service/windows/#pricing). Obviously if you do not need those features but still subscribed for the plan, you end up paying unnecessary for overprovisioned capability set which you will not use at all without compromising on the SLA's you are looking for. Select the lower plan which provides you more overlap on the services you need. 

It's like buying a mansion vs 2 floor house where your need is only for 2 floor house (4 households family). I am obviously paying extra.

Another aspect to look at what kind of resiliency, availability, scalability you need in your application/environment.
![image](https://user-images.githubusercontent.com/78099816/199600539-d3d9af9c-3286-45fd-af82-7fe8fe51f767.png)
**Figure 1.**

If you look at app service price in figure 1. You see the capacity selected in the plan will cost you $292.00 /monthly. But SLA of 99.95% is coming with it. Its baked into it.

![image](https://user-images.githubusercontent.com/78099816/199600695-6a96a9c4-c460-43c2-bb20-e9ce1c5e71ef.png)
**Figure 2.**


As compared to app services Cloud services are slightly cheaper for almost same cores/memory/storage  capacity. Even you can get your price more lowered by selecting different compute SKU's as well. However one thing here to remember is the SLA will only be applicable if you deploy 2 or more role instances, that means for ensuring SLA as  well as high availability you need to deploy minimum of 2 instances that doubles the cost what you are seeing in figure 2. 2X$204 = $408.00.Ultimately in this scenario you will end up paying more than what app services would have cost you.

### Summary, 
Pricing calculator is a great way to start understanding azure costs but you have to understand your workload extra and use that information to get to the right Azure Usage cost for your application. When it comes to choosing between both and giving the leisure of not official EOL for Azure Cloud Service Extended, both could be the landing zones as of today, not to diminish the futureproofing aspect as well.

![image](https://user-images.githubusercontent.com/78099816/199600889-49ac3e83-6efe-4b0d-9953-2bac7d5f962a.png)

