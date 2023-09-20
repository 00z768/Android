using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class UIManager : MonoBehaviour
{
    public Text Score;
    public Text BestScore;
    public Text GameOver;

    private int m_Score = 0;
    private int m_BestScore = 0;
    // Start is called before the first frame update
    void Start()
    {
        GameOver.enabled = false;
        m_Score = 0;
        m_BestScore = PlayerPrefs.GetInt("BestScore");
        RefershScore();
    }

    void RefershScore()
    {
        string str = string.Format("当前成绩:{0}",m_Score);
        Score.text = str;
        str = string.Format("最好成绩:{0}", m_BestScore);
        BestScore.text = str;
    }

    public void AddScore(int sc = 1)
    {
        m_Score += sc;
        if (m_Score > m_BestScore)
        {
            m_BestScore = m_Score;
            PlayerPrefs.SetInt("BestScore",m_BestScore);
        }
        RefershScore();
    }

    public void SetGameOver(bool IsEnd = true)
    {
        GameOver.enabled = IsEnd;
    }
    // Update is called once per frame
    void Update()
    {
        
    }
}
