
===============================
DEP XXXX: Deal Events Interface - Augmented
===============================

:DEP: XXXX
:Author: Rich Leto
:Implementation Team: Area - 51
:Shepherd: Mike Waters, Dmitriy Rozentul, Greg Armer, Vlad Kotlyar, Chaitanya Shah
:Status: Draft
:Type: Feature
:Created: 2019-01-22
:Last-Modified: 2019-01-22

.. contents:: Table of Contents
   :depth: 3
   :local:


Abstract
========

This is the administration system for the FNI-EVENT-NOTIFICATION-FRAMEWORK (DEP: 24).  The FNI-EVENT-NOTIFICATION-FRAMEWORK is comprised of Publishers, applications which can publish notifications and Subscribers, partners which are able to receive events through subscriptions. This system will be utilized for the creation and maintenance of publishers and subscribers.

Details
=======

The Event Notification Admin system (Eventing Admin) is hosted in AWS. The architecture consists of Dynamo to store publishers/subscribers data, Lambdas to update the Dynamo table and Cognito generate to client id/secrets which are used to access the publisher APIs.

An Eventing Admin user is created in Cognito.  This user will have the ability to create and edit publishers/subscribers.

At the time of registration, a Publisher is added to the dynamo table, a Cognito app_client is created, which will grant access to the publisher API and the events will for this publisher will be added to the A51-Event2Subscriptions table.


Dynamo Table (eventing_admin)
----------

**Schema**

:Authentication: Authentication model (oauth, key)
:aws:rep:deleting: Updated by AWS for Multi-Region Replication
:aws:rep:updatedatetime: - update by AWS for Multi-Region Replication
:aws:rep:updateregion: - update by AWS for Multi-Region Replication
:updatedby: - user which updated the record
:CallBackURL: -  Subscriber URL to push events
:ClientName (Key): - Name of the Publisher/Subscriber what should this align with? functional area, partner id?
:ClientType (Sort key): - either Publisher or Subscriber
:createdAt: - DateTime originally created
:DeliveryType: - Subscriber (delivery protocol)
:EventName: - Publisher - Name of Event
:GUID: - Where will we ge this from?  Do we need it?
:Options: - Publisher - used for ordering info
:Status: -  (A - Active, I - Inactive, P - Pending)

example:
 .. code-block:: JSON
  {
    "Authentication": "oauth",
    "aws:rep:deleting": "false",
    "aws:rep:updatedatetime": "1546466988.009001",
    "aws:rep:updateregion": "us-west-2",
    "updatedby": "ADM3212",
    "CallBackURL": "http://test.com",
    "ClientName": "one",
    "ClientType": "pub",
    "createdAt": "1546466988.009001",
    "DeliveryType": "http",
    "EventName": "A",
    "GUID": "ABSLSL",
    "Options": "{}",
    "Status": "A"
  }

Dynamo Table (A51-Event2Subscriptions)
----------
This table is updated when Publishers/Subscribers are added to the Eventing Admin table.  Event Name is the Key

**Schema**

:event_name: Name of Event registered in eventing_admin table
:subscribers: Partners that are subscribed to event

example:
 .. code-block:: JSON
  {
    "event_name": "A",
    "subscribers": "{"sub01", "sub03.fifo"},
  }
