

### BGP Notification Message

A **Notification message** is used when an error occurs or the peer connection is stopped. This message carries various error codes (e.g., timer expiry), error subcodes, and error information.

#### Fields in the Notification Message:
- **Error Code**: A 1-byte field that indicates the type of error. Every error is identified by a unique error code. Each error code can contain one or more error subcodes. If no appropriate error subcode is defined, a zero value is used for the Error Subcode field.
  
- **Error Subcode**: This field further defines the specific error under the given Error Code.

---

### **Message Header Error Sub Codes**
- **1** – Connection not synchronized.
- **2** – Incorrect message length.
- **3** – Incorrect message type.

---

### **Open Message Error Sub Codes**
- **1** – Unsupported Version Number.
- **2** – Incorrect Peer AS.
- **3** – Incorrect BGP Identifier.
- **4** – Unsupported Optional Parameter.
- **5** – Authentication Failure (RFC1771). It is deprecated in RFC4271. Please refer to RFC1771/RFC4271.
- **6** – Unacceptable Hold Time.

---

### **Update Message Error Sub Codes**
- **1** – Malformed Attribute List.
- **2** – Unrecognized Well-known Attribute.
- **3** – Missing Well-known Attribute.
- **4** – Attribute Flags Error.
- **5** – Attribute Length Error.
- **6** – Invalid ORIGIN Attribute.
- **7** – AS Routing Loop (RFC1771). It is deprecated in RFC4271. Please refer to RFC1771/RFC4271.
- **8** – Invalid NEXT_HOP Attribute.
- **9** – Optional Attribute Error.
- **10** – Invalid Network Field.
- **11** – Malformed AS_PATH.

---

### **Data Field**
The **Data** field is a variable-length field used to diagnose the reason for the NOTIFICATION. The contents of the Data field depend upon the **Error Code** and **Error Subcode**.

- The **length of the Data field** can be determined from the **Message Length** field using the following formula:
  
  `Message Length = 21 + Data L
