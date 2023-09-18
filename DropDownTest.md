using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class DropDownTest : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        //获取下拉组件
        Dropdown dropdown = GetComponent<Dropdown>();
        //获取组件的选项
        List<Dropdown.OptionData> options = dropdown.options;
        //修改选项
        options.Add(new Dropdown.OptionData("俄罗斯"));
        dropdown.options = options;
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
