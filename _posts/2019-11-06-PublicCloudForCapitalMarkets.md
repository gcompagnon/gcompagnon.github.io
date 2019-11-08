---
layout: post
title: "AFME paper on Public Cloud adoption in Capital Markets industry"
categories: cloud capital market
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [cloud capital market]
excerpt: Quick notes/bullet points about AFME paper on Public Cloud adoption in Capital Markets industry
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>



# Context
Theses bullet points are taken from an AFME paper about Public CLoud adoption/resistence in Capital Markets industry

The Association for Financial Markets in Europe (AFME) is the voice of all
Europe’s wholesale financial markets, providing expertise across a broad
range of regulatory and capital markets issues.

Twitter: 
@AFME_EU

[The Adoption of Public Cloud Computing in Capital Markets](https://www.afme.eu/reports/publications/details/The-Adoption-of-Public-Cloud-Computing-in-Capital-Markets)

[direct link on the AFME Paper](/pdf/20191106_AFME%20Cloud%20Paper%20November%202019%20Final.pdf)

# Modernisation and transformation IT Vision -> Cloud-First

One of the most problable IT strategy for the next 2-5 years is a Cloud-First approach as the continuity of the Virtualization of IT infrastructure. (Virtual Machines & Hypervisor solutions)

This approach is founded on a Secure, Hybrid , Multi-cloud offering.

Not a Bigbang approach, but a *complex*, distinct migration roadmap to plan:
   - from the straightforward "lift and shift" 
   - to the costy cloud-native rewriting applicationg)

Show stoppers are for each targets :

## Secure target (regulated)

* New Assessment process to define : 

<b>Public Cloud is not an another IT software product.</b>  No the same risk and contraints coming from regulation to respect (GDPR...) or to avoid (US Cloud Act)

page 11
"The bank’s cloud risk management framework is often more scrutinised that the product risk management framework, giving the bank more discretion in managing the product risk (which has several aspects of risks such as AML, financial, reputational etc) rather than cloud risks (which is technology, cyber & vendor risk)."

* Need to adapt outsourcing arrangements, for lower guaranty in case of fail of SLA (that are greater than ever, but not insured)

Public CLoud proposes greater SLA , but with no guarantee to activate in very rare / not zero case of failure.

This point could be solved by separate contract with cloud provider (not efficient) or ... with complex & costy HA architecture.
( as part of the Hybrid cloud architecture, with additional *switch* components like LoadBalancer)

## Multi-cloud

* No standardization in the service contracts & API = > need of an upper governance for speedup cloud adoption

signing separate contracts separately with each cloud providers , is not possible *for now* , as far as the services agreements are common for all customers.

* Legal / compliance requirements are not set right now, but cloud could be another form of outsourcing (IaaS-compute, Private) if data is put outside scope (ciphering or anonymising business data)

(technically , some solutions exist ... terraform , tactical library making a common layer upon the cloud provider services REST API )

<b>The mistake is to believe that Cloud would not be part of a Long-term IT strategy</b> - cloud services would be additional with bareMetal/ Virtualization / outsourced / SaaS solutions*

Not enough cloud providers lead to 
* Concentration risk => meaning low numbers of Cloud providers , is to leverage with the proposal of Cloud Regions (called HA zones)

## Hybrid strategy: 

* Internal datacenter should be cloudirized , and higher cost accepted targeting an inhouse Private managed Cloud

DevOps / Software Pipelines & Automation / containers (Docker , cri-o , Intel kata container,Container Solutions, ...)  are a prerequisite
Existing HPC framework (IBM Symphony) are orchestrating compute workloads on a distributed / hybrid cluster
Apache Kubernetes is the standard de facto for the orchestation of conteners

* need of training for non-IT / about Compliance, legal , data privacy


# other Links on documents
[EBA Outsourcing arrangements - guidelines & requirements](https://eba.europa.eu/sites/default/documents/files/documents/10180/2551996/38c80601-f5d7-4855-8ba3-702423665479/EBA%20revised%20Guidelines%20on%20outsourcing%20arrangements.pdf)

[European Commission, May 2019, The European Commission Cloud Strategy](https://ec.europa.eu/info/sites/info/files/ec_cloud_strategy.pdf)
