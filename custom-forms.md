# Custom Inno Setup forms

```pascal
[Run]
// You can't call functions with parameters outside of the [Code] section, so you have to use a helper function.
Filename: "C:\Windows\system32\cmd.exe"; Parameters: "/c ""keygen.exe {code:GenerateCmdline} > ""{app}\app.key"""""; WorkingDir: "{tmp}"; Flags: waituntilterminated runhidden; Description: "Generating license file..."; StatusMsg: "Generating license file..."

[Code]
// The variables are defined separately to make them easier to access outside of the TWizardPage object.
var
    NewPage: TWizardPage;
    SupportExpiryBox: TNewEdit;
    ExpiryBox: TNewEdit;
    LimitBox: TNewEdit;
    LicenseBox: TNewComboBox;
    EmailBox: TNewEdit;
    NameBox: TNewEdit;
    SupportExpiryLabel: TLabel;
    ExpiryLabel: TLabel;
    LimitLabel: TLabel;
    LicenseLabel: TLabel;
    EmailLabel: TLabel;
    NameLabel: TLabel;

function GenerateCmdline(Param: String): String;
begin
    // This function uses the values from the page to generate command line parameters
    Result := FmtMessage('-v 770 -u "%1" -e "%2" -n "%3" -t "%4" -l "%5" -s "%6"', [NameBox.Text, EmailBox.Text, LimitBox.Text, GetLicenseType(), ExpiryBox.Text, SupportExpiryBox.Text]);
end;

function NewPage_OnNextButtonClick(Sender: TWizardPage): Boolean;
begin
    // Result = True means it'll go to the next page. False means it will stay at the page.
    Result := True;
    if NameBox.Text = '' then
    begin
        MsgBox('Please enter a name for the license.', mbCriticalError, MB_OK or MB_DEFBUTTON1);
        Result := False;
    end
    else ...
    end;
end;

function NewPage_Create: TWizardPage;
begin
    // This creates the actual page. I created this using some tool.
    NewPage := CreateCustomPage(wpSelectComponents, 'Activation', 'Input your activation details here.');
    NewPage.OnNextButtonClick := @NewPage_OnNextButtonClick;
    NameLabel := TLabel.Create(NewPage);
    NameLabel.Parent := NewPage.Surface;
    NameLabel.Enabled := True;
    NameLabel.Visible := True;
    NameLabel.Left := ScaleX(36);
    NameLabel.Top := ScaleY(15);
    NameLabel.Width := ScaleX(39);
    NameLabel.Height := ScaleY(23);
    NameLabel.Font.Color := $000000;
    NameLabel.Color := $F0F0F0;
    NameLabel.Caption := 'Name:';
    NameBox := TNewEdit.Create(NewPage);
    NameBox.Parent := NewPage.Surface;
    NameBox.Enabled := True;
    NameBox.Visible := True;
    NameBox.Left := ScaleX(73);
    NameBox.Top := ScaleY(15);
    NameBox.Width := ScaleX(403);
    NameBox.Height := ScaleY(20);
    NameBox.Font.Color := $000000;
    NameBox.Color := $FFFFFF;
...
    LicenseBox := TNewComboBox.Create(NewPage);
    LicenseBox.Parent := NewPage.Surface;
    LicenseBox.Enabled := True;
    LicenseBox.Visible := True;
    LicenseBox.Left := ScaleX(135);
    LicenseBox.Top := ScaleY(75);
    LicenseBox.Width := ScaleX(121);
    LicenseBox.Height := ScaleY(21);
    LicenseBox.Font.Color := $000000;
    LicenseBox.ItemIndex := 0;
    LicenseBox.Color := $FFFFFF;
    LicenseBox.Items.Add('Named');
    LicenseBox.Items.Add('Computer');
    LicenseBox.Items.Add('Floating');
    Result := NewPage;
end;

procedure AddToReadyMemo(var Memo: string; Info, NewLine: string);
begin
    // This allows easy generation of the existing "Ready to Install" screen
    if Info <> '' then Memo := Memo + Info + Newline + NewLine;
end;

function UpdateReadyMemo(Space, NewLine, MemoUserInfoInfo, MemoDirInfo, MemoTypeInfo, MemoComponentsInfo, MemoGroupInfo, MemoTasksInfo: String): String;
begin
    // This updates the "Ready to install" screen with the previous component info, then with our custom form info.
    // built in bullshit
    AddToReadyMemo(Result, MemoUserInfoInfo, NewLine);
    AddToReadyMemo(Result, MemoDirInfo, NewLine);
    AddToReadyMemo(Result, MemoTypeInfo, NewLine);
    AddToReadyMemo(Result, MemoComponentsInfo, NewLine);
    AddToReadyMemo(Result, MemoGroupInfo, NewLine);
    AddToReadyMemo(Result, MemoTasksInfo, NewLine);

    // start of license info
    Result := Result + 'License information:' + NewLine + '      ';
    Result := Result + 'Name: ' + NameBox.Text + NewLine + '      ';
    Result := Result + 'Email: ' + EmailBox.Text + NewLine + '      ';
    Result := Result + 'License type: ' + GetLicenseType() + NewLine + '      ';
    Result := Result + 'License usage limit: ' + LimitBox.Text + NewLine + '      ';
    Result := Result + 'License expiry (in days): ' + ExpiryBox.Text + NewLine + '      ';
    Result := Result + 'Support expiry (in days): ' + SupportExpiryBox.Text + NewLine;
end;

procedure InitializeWizard();
begin
    // Create the page object when the Wizard is initializing
    NewPage_Create();
end;
```
