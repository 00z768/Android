using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class DropDownTest : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        //��ȡ�������
        Dropdown dropdown = GetComponent<Dropdown>();
        //��ȡ�����ѡ��
        List<Dropdown.OptionData> options = dropdown.options;
        //�޸�ѡ��
        options.Add(new Dropdown.OptionData("����˹"));
        dropdown.options = options;
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
