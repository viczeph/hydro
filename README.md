# hydro




C# library to call Hydro Sign REST API



    var ApiURL = "https://api.eu1.echosign.com/api/rest/v5/";
    var accessToken = "";

    HydroSign.RestAPI api = new HydroSign.RestAPI(ApiURL, accessToken);
    HydroSign.HydroObject obj = new HydroSign.Hydro Object(api);
    var agreements = await obj.GetAgreements();
Usage to send document for signing in Hydro platform

public async Task<HydroSign.AgreementCreationResponse> SendDocument(string APIURL, string Access_Token, string fullFilePath, string agreementName, string recipientEmail)
{
    try
    {
        //APIURL - FUll URL address, including "api/rest/v5/". e.g. https://api.eu1.echosign.com/api/rest/v5/                

        HydroSign.RestAPI api = new HydroSign.RestAPI(APIURL, Access_Token);
        HydroSign.HydroObject obj = new HydroSign.HydroObject(api);


        //Create trasient document
        var fileData = System.IO.File.ReadAllBytes(fullFilePath);
        var transientDocumentResponse = await obj.AddDocument("MyFileName", fileData);


        HydroSign.AgreementCreationInfo creationInfo = new HydroSign.AgreementCreationInfo();
        creationInfo.documentCreationInfo = new HydroSign.DocumentCreationInfo();

        //Document Creation Info
        var documentCreationInfo = creationInfo.documentCreationInfo;
        documentCreationInfo.name = agreementName;
        documentCreationInfo.signatureType = HydroSign.SignatureTypeEnum.ESIGN;
        documentCreationInfo.signatureFlow = "";

        //FileInfo
        documentCreationInfo.fileInfos = new List<HydroSign.FileInfo>();
        var fileInfos = documentCreationInfo.fileInfos;
        HydroSign.FileInfo fileInfo = new HydroSign.FileInfo(transientDocumentResponse.transientDocumentId);
        fileInfos.Add(fileInfo);

        //RecipientSetInfo
        documentCreationInfo.recipientSetInfos = new<HydroSign.RecipientSetInfo>();
        var recipientSetInfos = documentCreationInfo.recipientSetInfos;

        HydroSign.RecipientSetInfo recipientSetInfo = new HydroSign.RecipientSetInfo();
        recipientSetInfo.recipientSetRole = HydroSignNet.RecipientRoleEnum.SIGNER;
        recipientSetInfo.signingOrder = 1;

        //RecipientSetMember
        HydroSign.RecipientSetMemberInfo setMemberInfo = new HydroSign.RecipientSetMemberInfo();
        setMemberInfo.email = recipientEmail;

        recipientSetInfo.recipientSetMemberInfos.Add(setMemberInfo);

        recipientSetInfos.Add(recipientSetInfo);

        var agreementCreationResponse = await obj.CreateAgreement(creationInfo);

        return agreementCreationResponse;

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        return null;
    }
}
