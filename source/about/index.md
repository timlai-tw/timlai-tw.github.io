---
title: 關於我
date: 2024-08-30 12:27:15
comment: true
page_cover: /images/banner/its-banner.png
---


```csharp
using System;
using System.Collections.Generic;

[Serializable]
public record TimLai : ICloudArchitect, IDevOpsEnthusiast
{
    public string Name { get; init; } = "Tim Lai";
    public string Title = null;
    public string Email = "yutai.lai@gmail.com";    
    public string Profession => "軟體開發與系統架構設計";
    public int YearsOfExperience => 20;

    public string Summary => $"Hi，我是 {Name}！軟體開發是我一直以來從事的領域，也是興趣所在。"+
                              "我熱愛學習新技術並將其應用在實際專案中，"+
                              "在這 AI 蓬勃發展的時代，如何善用 LLMs 與各項工具來提升軟體開發品質與效率，"+
                              "將會是我未來持續探索的方向。";

    public List<string> Skills { get; } = new()
    {
        "Azure DevOps 導入與應用",
        "Azure 解決方案架構設計",
        "SPFx & Microsoft Graph",
        "GitHub Copilot 整合應用",
        "專案管理與敏捷方法論"
    };

    public IReadOnlyList<string> Interests => new[]
    {
        "硬筆書法 🖋️",
        "羽毛球 🏸",
        "咖啡/烘豆/手沖/拉花 ☕️",
        "人文紀錄 📸",
    };
    
    public IReadOnlyList<string> Certifications => new[]
    {
        "ISO/IEC 27001:2022 Lead Auditor (Information Security Management Systems)",
        "PMI Project Management Professional (PMP)®",
        "PMI Agile Certified Practitioner (PMI-ACP)®",
        "PMI Professional in Business Analysis (PMI-PBA)®",
        "Gemini Certified Educator",
        "Kanban Management Professional (KMP)®",
        "Team Kanban Practitioner (TKP)®",
        "Microsoft 365 Certified: Fundamentals",
        "Microsoft Certified: Azure AI Engineer Associate",
        "Microsoft Certified: Azure Developer Associate",
        "Microsoft Certified: Azure Fundamentals",
        "Microsoft Certified: DevOps Engineer Expert",
        "Microsoft Certified: Microsoft Azure Administrator",
        "Microsoft Certified: Security, Compliance, and Identity Fundamentals"
    };

    public Uri LinkedIn => new("https://www.linkedin.com/in/tim-lai-6886b1135/");

    public string Motto => GetMotto();

    public event EventHandler? DeploySuccess;

    public void Deploy()
    {
        Console.WriteLine("Deploying MyLife pipeline...");
        DeploySuccess?.Invoke(this, EventArgs.Empty);
    }

    private string GetMotto() => "If something is difficult or painful, do it more often.";
}

public interface ICloudArchitect
{
    string Title { get; }
    void Deploy();
}

public interface IDevOpsEnthusiast
{
    List<string> Skills { get; }
    event EventHandler? DeploySuccess;
}
```

