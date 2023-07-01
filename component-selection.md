# Disable other components when one is checked

If you're building an installer that has both normal and portable install methods, you'd likely want any data to be alongside the application, instead of in another directory. This is how to disable other components when one is checked.

```pascal
[Code]
var
    ComponentsVisited: Boolean;

procedure UpdateComponents;
begin
    with WizardForm.ComponentsList do
    begin
        if IsComponentSelected('ComponentThat/ConflictWith/Others') then
        begin
            CheckItem(indexofitemtocheck, coCheck);
        end;
        ItemEnabled[indexofitemtouncheck] := not IsComponentSelected('ComponentThat/ConflictWith/Others');
        Invalidate;
    end;
end;

procedure ComponentsClickCheck(Sender: TObject);
begin
    DefaultCompClickCheck(Sender);
    UpdateComponents;
end;

procedure ComponentsTypesComboChange(Sender: TObject);
begin
    DefaultCompTypeChange(Sender);
    UpdateComponents;
end;

procedure CurPageChanged(CurPageID: Integer);
begin
    if (CurPageID = wpSelectComponents) and not CompPageVisited then
    begin
        CompPageVisited := True;
        UpdateComponents;
    end;
end;

procedure InitializeWizard();
begin
    DefaultCompClickCheck := WizardForm.ComponentsList.OnClickCheck;
    WizardForm.ComponentsList.OnClickCheck := @ComponentsClickCheck;
    DefaultCompTypeChange := WizardForm.TypesCombo.OnChange;
    WizardForm.TypesCombo.OnChange := @ComponentsTypesComboChange;
end;
```
