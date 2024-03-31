
# XCP Command Codes

In the XCP protocol, each command is identified by a specific command code, which is transmitted as the first byte of an XCP packet, known as the Package Identifier (PID). These command codes are standardized by ASAM's specification of the XCP protocol. Below is a table containing a set of XCP commands along with their corresponding command codes. By analyzing the first byte of each XCP packet, you can identify the respective XCP commands as listed in the table.

| Command             | Command Code |
| :------------------ | ------------ |
| GET_STATUS          | 0xFD         |
| SYNCH               | 0xFC         |
| GET_COMM_MODE_INFO  | 0xFB         |
| GET_ID              | 0xFA         |
| SET_REQUEST         | 0xF9         |
| GET_SEED            | 0xF8         |
| UNLOCK              | 0xF7         |
| SET_MTA             | 0xF6         |
| UPLOAD              | 0xF5         |
| SHORT_UPLOAD        | 0xF4         |
| BUILD_CHECKSUM      | 0xF3         |
| TRANSPORT_LAYER_CMD | 0xF2         |
| USER_CMD            | 0xF1         |
| DOWNLOAD            | 0xF0         |
| POS_ACK (RES)       | 0xFF         |
| ERR                 | 0xFE         |

By referring to this table, you can easily determine the XCP command being used in a given XCP packet by examining the value of the first byte, which represents the command code.
