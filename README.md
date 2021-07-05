# Fields-without-permissions
A script to list all the fields which don't have FLS permission.


{
    
    Map<String, Schema.SObjectType> lowerCaseGlobalMap = Schema.getGlobalDescribe();

    Map<String, List<String>> returnVal = new Map<String, List<String>>();

    String all = '';
    for(String s : lowerCaseGlobalMap.keySet()){

        Schema.DescribeSObjectResult dsr = lowerCaseGlobalMap.get(s).getDescribe();
        if(dsr.isCustomSetting() || !dsr.isQueryable()){
            continue; 
        }
        Map<string, Schema.Sobjectfield>  fieldMap = dsr.fields.getMap();
        String obj = s;

        List<String> fieldList = new List<String>();
        for(String f : fieldMap.keySet()){

            String val = obj + '.' + f;
            if(String.isEmpty(all))
                all = val;
            else
                all = all + '\n' + val;


        }
        returnVal.put(s, fieldList);

    }

    Blob b = blob.valueOf(all);

    Messaging.SingleEmailMessage message = new Messaging.SingleEmailMessage();
    message.setToAddresses(new String[] { '<-- Email Address -->' });
    List<Messaging.EmailFileAttachment> attachments = new List<Messaging.EmailFileAttachment>();
    Messaging.EmailFileAttachment efa = new Messaging.EmailFileAttachment();
    efa.setFileName('sample');
    efa.setBody(b);
    efa.contenttype  = 'text/plain';
    attachments.add(efa);
    message.setFileAttachments(attachments);
    message.setHtmlBody('asdasd');
    Messaging.sendEmail(new Messaging.SingleEmailMessage[] { message });
}
