%%%
title = "Structured vacation notices"
area = "ART"
workgroup = "SML"

[seriesInfo]
name = "Internet-Draft"
value = "draft-happel-sml-structured-vacation-notices-00"
stream = "IETF"
status = "standard"

[[author]]
initials = "H.-J."
surname = "Happel"
fullname = "Hans-Joerg Happel"
organization = "audriga GmbH"
  [author.address]
   email = "happel@audriga.com"
   uri = "https://www.audriga.com"

%%%

.# Abstract 

This document describes a machine-readable format for vacation notice messages. It is supposed to be used in conjuction with conventional, human-readable vacation notices.

{mainmatter}

# Introduction

A "vacation notice" (also known as "out-of-office notice" [@RFC3834][@RFC5598] or "-reply") is a special type of automated message which is sent in response to incoming mail, if the recipient is absent or otherwise unable to answer immediately.

Its content is written by the absentee in advance, before it is sent automatically by a MUA. Many MUAs also allow to specify the exact time period during which incoming messages should be automatically answered with a vacation notice.

Vacation notices are not standardized as such. [@RFC5230] specifies an extension to the Sieve email filtering language. While vacation notices are partially formalized on the side of the absentee (e.g., start and end date), they consist of only human-readable language for their recipient.

The goal of this specification is to allow absentees to include a machine-readable version of the vacation notice, such that reicipients can be assisted by software when dealing with vacation notices.

While this specification may be used stand-alone, is aims to be compliant to the "structured email" specification ([I-D.ietf-sml-structured-email-00]) and its trust and securirty recommendations ([I-D.happel-structured-email-trust-00]).

# Conventions Used in This Document

The term "message" refers to "electronic mail messages" or "emails" as specified in [@RFC5322].

The term "Message User Agent" (MUA) denotes an email client application as per [@RFC5598]. Based on the role of the communication partners, one can further distiguish into "Recipient MUA" (rMUA) and "Author MUA" (aMUA).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [@RFC2119] [@RFC8174] when, and only when, they appear in all capitals, as shown here.

# Data model

The minimum data for a vacation notice is the actual notice content as specified by the absentee. It might be as short as "I am currently out of office".

Many systems also allow to specify a time range during which a vacation notice should be sent. As for the Sieve vacation extension ([@RFC5230]), it is often used in conjuction with a "currentdate" test ([RFC5260]) to achieve this.

Notably, this time range must not exactly match the actual absence time, even though this is mostly the case in practice.

Vacation notice content may also contain information about if a message is automatically forwarded to a replacement person, or details about replacement persons to contact.

The following snippet shows a potential extension to the [@SchemaOrg] vocabulary in [@JSONLD] format.

	{
		"@context": "https://schema.org",
		"@type": "OutOfOffice",
		"start": "2023-08-15",
		"end": "2023-08-22",
		"isForwarded": false,
		"replacement": [
		    {
		        "@type": "OutOfOfficeReplacement",
		        "name": "John Doe",
		        "topic": "Project A",
		        "email": "john@doe.com",
		        "phone": "+1234567890"
		    },
		    {
		        "@type": "OutOfOfficeReplacement",
		        "name": "Jane Doe",
		        "topic": "Project B",
		        "email": "jane@doe.com",
		        "phone": "+9876543210"
		    }
		],
		"note": "Some text"
	}

```
Note: This is a preliminary specification only. Do not use in practice.
```

# Use cases

For the use cases, we distinguish the absentee, willing to answer incoming messages with a vacation notice, and communication partner(s), which want to send messages to the absentee.

## Absentee

The absentee can make use of structured vacation notices in common vacation notice autoreplies but also in regular messages.

### Outgoing vacation notice

For including a structured vacation notice, a JSON-LD snippet using the "OutOfOffice"-type defined in the previous section needs to be embedded in the text/html representation of the vacation notice email.

In systems using the Sieve "vacation" extension ([@RFC5230]), text/html body parts are supported when using the parameter to include MIME content (":mime").

If the user interface already allows to set date ranges, the structured vacation notice data may be added without extra user effort.

### Outgoing preemptive vaction notice

Machine-readable markup allows absentees to also include structured vacation notices in regular messages sent before or during their absence.

## Communication partner

### Incoming vacation notice

If an incoming vacation notice contains structured vacation notice data, the MUA of the communication partner MAY extract and store this data.

Since the MUA can make sense of the structured vacation notice, it MAY also employ various forms of user assistance at its own discretion, such as:

* Highlight the absence in a special way
* Allow to take certain action (set reminder, forward message to replacement person)
* Inform user when composing an email to a person known to be absent

### Incoming preemtive vacation notice

As described before, the absentee may decide to add structured vacation notices in regular messages sent before or during her absence.

When receiving such messages, the MUA of the communication partner MAY extract and store the structured vacation notice and MAY highlight the absence information.

### Message composition

If a communication partner wants to send a message to a person, for which a structured vacation notice has been received earlier, the MUA MAY notify its user about this upcoming or ongoing absence.


# Related use cases

There are some use cases which are somehow related to "vacation notices", mostly by providing automated messages about a certain status of the recipient.

Those related use cases may be worth further consideration within the design space of this draft.

## Change of address

As highlightd in [RFC3834], an automated response might also convey the fact that an email address has become obsolete or changed.

## New contact data

Beyond the email addresses, senders sometimes highlight updated information in the signature of a message, such as:

* Updated postal address
* Updated phone number


# Implementation guidance

The following points should be considered when implementing (structured) vacation notices from a sender and recipient-side processing perspective.

## Sending

### Leave optional

Adding structured data to a vacation notice should be left as a choice to the user. A MUA SHOULD not add structured data to vacation notices without informing the user.

### Provide alternative representations

A vacation notice including structured data should always include an alternative, human-readable version of the vacation notice.

## Processing

### Igore past time ranges

Ignore structured vacation notices with time ranges in the past.

### Prefer latest vacation time range

If multiple structured vacation notices exist for a user, prefer the one from the most recent incoming message.

### Strip when forwarding

In the case of preemptive structured vacation notices, strip the structured data  from the message when it is forwarded to a third party by the user.	

# Implementation status

< RFC Editor: before publication please remove this section and the reference to [@RFC7942] >

This section records the status of known implementations of the protocol defined by this specification at the time of posting of this Internet-Draft, and is based on a proposal described in [@RFC7942]. The description of implementations in this section is intended to assist the IETF in its decision processes in progressing drafts to RFCs. Please note that the listing of any individual implementation here does not imply endorsement by the IETF. Furthermore, no effort has been spent to verify the information presented here that was supplied by IETF contributors. This is not intended as, and must not be construed to be, a catalog of available implementations or their features. Readers are advised to note that other implementations may exist.

According to [@RFC7942], "this will allow reviewers and working groups to assign due consideration to documents that have the benefit of running code, which may serve as evidence of valuable experimentation and feedback that have made the implemented protocols more mature. It is up to the individual working groups to use this information as they see fit".

## Structured Vacation Notice plugin for Roundcube Webmail

This draft is implemented in an open source plugin for the Roundcube Webmail system [@RC-SVN], partly based on the Roundcube managesieve plugin.

# Security considerations

In particular when using structured vacation notices in conjunction with the Sieve filtering language, the security considerations of the corresponding RFCs should be taken into account:

* Sieve base specification [@RFC5228]
* Sieve Vacation extension [@RFC5230]
* [I-D.happel-structured-email-trust-00]

# Privacy considerations

Vacation notices expose certain potentially sensitive information to third parties, such as absense times, absense reasons and organizational details (such as replacement staff and their contact information).

For this reason, absentees typically are free to decide how much information they expose in the writte n text of their vacation notice.

Accordingly, software systems SHOULD leave absentees the same level of freedom when adding structured vacation notices and e.g., not enforce the inclusion of certain information or even do so implicitly.

Information exposure might also be limited by restricting the usage of structured vacation notices to certain communication partners (e.g., using address book information [@RFC6134] as discussed in [@RFC6133]).

# IANA Considerations

This document has no IANA actions at this time.

# Appendix (vCard properties)

The following vCard "X-Properties" are currently used for internal storage of structured vacation notice data for external contacts of the mailbox user.

	X-OOF-UPDATED:2023-10-01
	X-OOF-START:2023-10-01
	X-OOF-END:2023-11-01
	X-OOF-IS-FORWARDED:false
	X-OOF-REPLACEMENT:Jane Doe,Marketing,jane.doe@corp.com,+1234567-89
	X-OOF-REPLACEMENT:John Doe,Development,john.doe@corp.com,+1234567-99
	X-OOF-NOTE:I am out of office please reach my replacement instead

{backmatter}

<reference anchor="JSONLD" target="https://www.w3.org/TR/json-ld/">
   <front>
      <title>JSON-LD 1.1</title>
      <author>
        <organization>W3C JSON-LD Working Group</organization>
      </author>
      <date>2020</date>
   </front>
</reference>

<reference anchor="SchemaOrg" target="https://schema.org/">
   <front>
      <title>Schema.org</title>
      <author>
        <organization>W3C Schema.org Community Group</organization>
      </author>
      <date>2023</date>
   </front>
</reference>

<reference anchor="RC-SVN" target="https://github.com/audriga/roundcube-structured-vacation-notice/">
   <front>
      <title>Structured Vacation Notice plugin for Roundcube Webmail</title>
      <author>
        <organization>audriga GmbH</organization>
      </author>
      <date>2024</date>
   </front>
</reference>

