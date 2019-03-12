# MVC_EnsuringUserHasRightsToData
MVC C# Tip to ensure the User has rights to the data that is being requested

As an example in a .Net MVC site, to edit a record the URL may look like this:
~/Clients/edit/1

If the user that is Authenicated to your site is allowed to see record 1, then that's okay.
But what if the user manually types ~/Clients/edit/100, the Edit screen would appear for Client Record ID 100, maybe Client 100 beongs to a totally different user. Thats a major security violation.
The security needed must ensure user has rights to only access/modify data belonging to his/her clients.


One Simple Straight Forward Way to making Sure User has Access to the Data being Requested.
Add an UserID or OrganizationID field to each Record, this field signifies that data belongs to them.

Then add a Helper Class which checks that the user's UserID or OrganizationID appears in that record before it is opened.

The Coding

```

[HttpPost]
public ActionResult SeeClient(Client model)
{
  var SecurityChecker = new SecurityCheckerHelper(); /// This instantiates the Helper Class
  var isOkayToAccess = SecurityChecker.DoesCurrentUserHaveAccessToClient(myOrganizationID, model.ID); // model.ID = Client Record ID
  if(isOkayToAccess)
  {
     // Passed Security, Okay to access record
     //Continue with Editing or Saving or Whatever processing     
     return RedirectToAction("ClientActionCompleted");
  }
  
  return View("NotAuthorized");
}

```

SecurityCheckerHelper Class looks something like this

```

public class SecurityCheckerHelper
{
      public bool DoesCurrentUserHaveAccessToClient(string OrganizationID, int ID)
        {
        var isOKAY = false;
        var Clients = db.Clients.Where(a => a.organizationid == OrganizationID).Where(a => a.id == ID);
        if (Clients != null)
        {
        isOkay = true;
        return isOkay;
        }
        
        return isOkay;
        }
}

```
