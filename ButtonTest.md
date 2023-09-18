using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro;

public class ButtonTest : MonoBehaviour
{
    public InputField inputField;
    public TMP_InputField newInputField;
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
    public void ButtonClick()
    {
        Debug.Log("click");
    }

    public void TextChanged()
    {
        Debug.Log(inputField.text);
    }
}

