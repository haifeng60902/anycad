Integrate AnyCAD to your .Net application.


# Initialize Application #
```
public AnyCAD.Platform.Application app = new AnyCAD.Platform.Application();

// Initialize the AnyCAD before calling other APIs
app.Initialize();
```

```
// Release the resource before exiting your application
app.Destroy();
```

# Manage Document #
## Create ##
```
public AnyCAD.Platform.Document doc = new AnyCAD.Platform.Document();
doc.NewDocument();
```

## Open ##
```
            OpenFileDialog dlg = new OpenFileDialog();
            dlg.Filter = "AnyCAD Viewer (*.asd)|*.asd";

            if (DialogResult.OK == dlg.ShowDialog())
            {
                doc.OpenDocument(dlg.FileName);
            }
```
## Save ##
```
            SaveFileDialog dlg = new SaveFileDialog();
            dlg.Filter = "AnyCAD Scene Desinger(*.asd)|*.asd";

            if (DialogResult.OK == dlg.ShowDialog())
            {
                doc.SaveDocument(dlg.FileName);
            }
```
## Load Type ##
Use Document::LoadComponent(fileName, shared) to load the smart model into the document as a TypeInstance. if shared is true, AnyCAD will reuse the existing model that has been loaded.

Use InstanceElement to create an instance of the model.

Use Document::AddElement to add the instance to the document.
```
            OpenFileDialog dlg = new OpenFileDialog();
            dlg.Filter = "AnyCAD Smart Model (*.asmm)|*.asmm";

            if (DialogResult.OK == dlg.ShowDialog())
            {
                // Load asmm file to document
                int nId = doc.LoadComponent(dlg.FileName, true);
                if (nId > 0)
                {
                    // Create 10 instance sharing the same Type.
                    for (int ii = -10; ii < 10; ++ii)
                    {
                        InstanceElement insElem = doc.CreateInstance(nId);
                        insElem.SetPosition(new Vector3(100 * ii, 100 * ii, 0));
                        insElem.SetName("Instance:" + ii.ToString());
                        doc.AddElement(insElem.AsElement());
                    }

                }
            }
```

## Find Elements ##
ElementTable
### Get all Instances ###
```
ElementTable table = doc.FindElementTable(ElementTable.InstanceElementTableId());
HashSet<int> ids = table.GetElementIds();
for each(int id in ids)
{
   Element elem = doc.FindElement(id);
   InstanceElement instance = elem.AsInstanceElement();
}
```
### Get all Types ###
```
ElementTable table = doc.FindElementTable(ElementTable.TypeElementTableId());
HashSet<int> ids = table.GetElementIds();
for each(int id in ids)
{
   Element elem = doc.FindElement(id);
   TypeElement type= elem.AsTypeElement();
}
```
### Find the Type used by Instance ###
```
InstanceElement ins = ...
int typeId = ins.GetTypeElementId();
Element elem = doc.FindElement(typeId);
TypeElement typeElem = elem.AsTypeElement()

```

## Modify Element ##
### Transform ###
Call InstanceElement::Regenerate((uint)RegenerateHint.Hint\_Transform) to apply the changes
```
            ElementTable elemTable = doc.FindElementTable(ElementTable.InstanceElementTableId());
            if (elemTable == null)
                return;

             Vector3 offset = new Vector3(0, 0, 100);
            System.Collections.Generic.HashSet<int> ids = elemTable.GetElementIds();
            foreach( int id in ids)
            {
                Element elem = doc.FindElement(id);
                InstanceElement ins = elem.AsInstanceElement();

                ins.SetPosition(ins.GetPosition() + offset);
                uint nRegenHint = (uint)RegenerateHint.Hint_Transform;
                ins.Regenerate(nRegenHint);
            }

            view.UpdateView();
```
## Parameters ##
### Find Parameters ###
```
ParameterItemSet paramSet = instance.GetParameters();
ParameterItem item = paramSet.FindParameter("ParamId");
//....

// iterate all the parameters
ParameterIterator itr = paramSet.GetParameterIterator();
for( itr.Begin; !itr.IsEnd(); itr.Next() )
{
  ParameterItem item = itr.Current();
  //....
}
```


# Manage 3d viewer #
## Create the 3d view ##
```
public AnyCAD.Presentation.View3d view = new AnyCAD.Presentation.View3d();

// Use your window's handle, and the document you create to initialize the view
view.Initialize(Handle, doc);

// resize the view to fit the window size
view.Resize(Width, Height);
```
## Draw View ##
```
// Call UpdateView when redrawing
view.UpdateView();
```
## Add mouse event handler ##
```
        private void OnMouseDown(object sender, MouseEventArgs e)
        {
            view.OnMouseDown(e);
        }

        private void OnMouseMove(object sender, MouseEventArgs e)
        {
            view.OnMouseMove(e);
        }

        private void OnMouseUp(object sender, MouseEventArgs e)
        {
            view.OnMouseUp(e);
        }

        private void OnMouseWheel(object sender, MouseEventArgs e)
        {
            view.OnMouseWheel(e);
        }
```
## Destroy View ##
```
// Destroy the view before closing the window
view.Destroy();
```

# Handle Event #
## On Add Element ##
When adding TypeElement and InstanceElement, OnAddElement is triggered.
If you are only interested in InstanceElement, you can write the handler below:
```
private void OnAddElement(Element pElement)
        {
            InstanceElement insElem = pElement.AsInstanceElement();
            if( insElem != null )
                MessageBox.Show(insElem.GetId().ToString(), "Add Element");
        }
```

## On Select Element ##
When selecting a model in view, OnSelectElement event is triggered.
```
        private void OnSelectElement(System.Collections.Generic.List<int> ids)
        {
            if( ids.Count == 1 )
            {
                Element elem = doc.FindElement(ids[0]);
                if( elem != null )
                {
                    MessageBox.Show(elem.GetName(), "Select Element");
                }
            }
        }
```

## On Update Element ##
After moving an instance, you can receive the OnUpdateElement event
```
       view.OnUpdateElement += OnUpdateElement;

        private void OnUpdateElement(Element pElement)
        {
            InstanceElement insElem = pElement.AsInstanceElement();
            if (insElem != null)
                MessageBox.Show(insElem.GetId().ToString(), "Moved!");
        }
```