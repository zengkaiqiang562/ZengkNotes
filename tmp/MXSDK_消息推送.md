Logcat的TAG标签： mxpush | PushConnectService | mxmessage | PushDataHandleService | MXPreferenceEngine

可能通过广播 "com.minxing.action.message.refresh" 通知接收到聊天消息

接收文字
04-21 18:02:28.926 13389-25868/? E/mxpush: 19-04-21 18:02:28.943-[MXMC] [messageArrived]
04-21 18:02:28.936 13389-25868/? D/PushConnectService:  [messageArrived]
04-21 18:02:28.936 13389-25868/? D/PushConnectService: messageArrived----------
04-21 18:02:28.936 13389-25868/? E/mxmessage: 19-04-21 18:02:28.945-[push] messageArrived----
04-21 18:02:28.936 1312-2856/? I/ActivityManager: retrieveServiceLocked, callerApp: ProcessRecord{5f858be 13389:com.hnjcxxdjex.client:push/u0a133}, flags: 400
04-21 18:02:28.936 13246-13246/? I/PushDataHandleService: onCreate
04-21 18:02:28.936 1312-2856/? I/ActivityManager: bindServiceLocked, r: ServiceRecord{3aee48c u0 euid: 0 com.hnjcxxdjex.client/com.minxing.kit.internal.core.PushDataHandleService}, euid: 0
04-21 18:02:28.936 13246-13246/? I/PushDataHandleService: onStartCommand
04-21 18:02:28.936 13246-13246/? I/PushDataHandleService: handleIntent
04-21 18:02:28.936 13246-13246/? E/mxpush: 19-04-21 18:02:28.950-[PD] receive msg type private_message
04-21 18:02:28.936 13246-13246/? E/mxmessage: 19-04-21 18:02:28.951-[push] [handleMessage]messageType  is private_message
04-21 18:02:28.946 13246-29152/? E/mxpush: 19-04-21 18:02:28.954-[CH] handle and message  id is 968 and seq is 59
04-21 18:02:28.946 13246-29152/? E/mxpush: 19-04-21 18:02:28.959-[DB] insert msg id 968
04-21 18:02:28.946 13246-29152/? E/mxmessage: 19-04-21 18:02:28.959-[push]  insert success msg id 968 
04-21 18:02:28.946 13246-29152/? E/mxpush: 19-04-21 18:02:28.962-[MXPreferenceEngine] [saveConversationLastMessageID]messageID:968
04-21 18:02:28.946 13246-29152/? E/MXPreferenceEngine: [savePreferenceValue]mDatabase:net.sqlcipher.database.SQLiteDatabase@5ea66c
04-21 18:02:28.956 13246-29153/? E/mxpush: 19-04-21 18:02:28.965-[doRepaire]
04-21 18:02:28.976 13246-29152/? E/mxpush: 19-04-21 18:02:28.989-[MXPreferenceEngine] [saveConversationLastMessageSeq]seq:59
04-21 18:02:28.976 13246-29152/? E/MXPreferenceEngine: [savePreferenceValue]mDatabase:net.sqlcipher.database.SQLiteDatabase@5ea66c
04-21 18:02:28.996 13246-29152/? I/SendBroadcastPermission: action:com.minxing.action.message.refresh, mPermissionType:0

接收图片
04-21 18:09:31.526 13389-25868/? E/mxpush: 19-04-21 18:09:31.534-[MXMC] [messageArrived]
04-21 18:09:31.526 13389-25868/? D/PushConnectService:  [messageArrived]
04-21 18:09:31.526 13389-25868/? D/PushConnectService: messageArrived----------
04-21 18:09:31.526 13389-25868/? E/mxmessage: 19-04-21 18:09:31.535-[push] messageArrived----
04-21 18:09:31.546 1312-2858/? I/ActivityManager: retrieveServiceLocked, callerApp: ProcessRecord{5f858be 13389:com.hnjcxxdjex.client:push/u0a133}, flags: 400
04-21 18:09:31.546 13246-13246/? I/PushDataHandleService: onCreate
04-21 18:09:31.546 1312-2858/? I/ActivityManager: bindServiceLocked, r: ServiceRecord{ff0a0ff u0 euid: 0 com.hnjcxxdjex.client/com.minxing.kit.internal.core.PushDataHandleService}, euid: 0
04-21 18:09:31.546 13246-13246/? I/PushDataHandleService: onStartCommand
04-21 18:09:31.546 13246-13246/? I/PushDataHandleService: handleIntent
04-21 18:09:31.556 13246-13246/? E/mxpush: 19-04-21 18:09:31.567-[PD] receive msg type private_message
04-21 18:09:31.556 13246-13246/? E/mxmessage: 19-04-21 18:09:31.567-[push] [handleMessage]messageType  is private_message
04-21 18:09:31.556 13246-29705/? E/mxpush: 19-04-21 18:09:31.573-[CH] handle and message  id is 969 and seq is 60
04-21 18:09:31.566 13246-29705/? E/mxpush: 19-04-21 18:09:31.578-[DB] insert msg id 969
04-21 18:09:31.566 13246-29705/? E/mxmessage: 19-04-21 18:09:31.578-[push]  insert success msg id 969 
04-21 18:09:31.566 13246-29705/? E/mxpush: 19-04-21 18:09:31.581-[MXPreferenceEngine] [saveConversationLastMessageID]messageID:969
04-21 18:09:31.566 13246-29705/? E/MXPreferenceEngine: [savePreferenceValue]mDatabase:net.sqlcipher.database.SQLiteDatabase@5ea66c
04-21 18:09:31.576 13246-29706/? E/mxpush: 19-04-21 18:09:31.585-[doRepaire]
04-21 18:09:31.586 13246-29705/? E/mxpush: 19-04-21 18:09:31.601-[MXPreferenceEngine] [saveConversationLastMessageSeq]seq:60
04-21 18:09:31.586 13246-29705/? E/MXPreferenceEngine: [savePreferenceValue]mDatabase:net.sqlcipher.database.SQLiteDatabase@5ea66c
04-21 18:09:31.616 13246-29705/? I/SendBroadcastPermission: action:com.minxing.action.message.refresh, mPermissionType:0
04-21 18:09:31.616 1312-2858/? I/ActivityManager: collectReceiverComponents for intent: Intent { act=com.minxing.action.message.refresh flg=0x10 }, receivers: null


发送文字
04-21 18:17:42.786 13246-13246/? E/mxdebug: 19-04-21 18:17:42.796-[mesage_send]textSendButton click
04-21 18:17:42.816 13246-30158/? E/mxdebug: 19-04-21 18:17:42.825-[mesage_send]before  sendReplyMessage
04-21 18:17:42.816 13246-30158/? E/mxdebug: 19-04-21 18:17:42.826-[mesage_send]before  MXHttpClientAsync execute
04-21 18:17:42.826 13246-13334/? E/mxmessage: 19-04-21 18:17:42.840-[HrAuth][getCustomHeaders]
04-21 18:17:42.826 13246-13334/? E/mxdebug: 19-04-21 18:17:42.841-[mesage_send]sent to server:http://47.104.140.108/api/v1/conversations/20020719/messages