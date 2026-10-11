loadstring(game:HttpGet(("https://raw.githubusercontent.com/daucobonhi/Ui-Redz-V2/refs/heads/main/UiREDzV2.lua")))()

       local Window = MakeWindow({
         Hub = {
         Title = "Bee hub",
         Animation = "Youtube: Bee hub"
         },
        Key = {
        KeySystem = false,
        Title = "Key premium",
        Description = "",
        KeyLink = "",
        Keys = {"BEE_3667_VIP"},
        Notifi = {
        Notifications = true,
        CorrectKey = "Running the Script...",
       Incorrectkey = "The key is incorrect",
       CopyKeyLink = "Copied to Clipboard"
      }
    }
  })

       MinimizeButton({
       Image = "http://www.roblox.com/asset/?id=96038703917511",
       Size = {60, 60},
       Color = Color3.fromRGB(10, 10, 10),
       Corner = true,
       Stroke = false,
       StrokeColor = Color3.fromRGB(255, 0, 0)
      })
      
------ Tab
     local Tab1o = MakeTab({Name = "Script Farm"})
     local Tab2o = MakeTab({Name = "sea event"})
------- BUTTON
    
    AddButton(Tab1o, {
     Name = "Bee hub"
    Callback = function()
	  local Settings = {
  JoinTeam = "Pirates"; -- Pirates/Marines
  Translator = true; -- true/false
}
-- ====================================================================
-- PHẦN 1: KHỞI TẠO BIẾN CÔNG TẮC BẬT/TẮT (Liên kết với Toggle của UI)
-- ====================================================================
_G.AutoAttack = true -- Mặc định BẬT. Khi đưa vào UI, nút bấm sẽ chỉnh biến này thành true/false

-- ====================================================================
-- PHẦN 2: DÒ TÌM REMOTE EVENT ẨN VÀ LẤY ID BẢO MẬT (Đã fix lỗi crash do thiếu thư mục)
-- ====================================================================
local v1 = next
local v2 = {
    game.ReplicatedStorage:FindFirstChild("Util"),
    game.ReplicatedStorage:FindFirstChild("Common"),
    game.ReplicatedStorage:FindFirstChild("Remotes"),
    game.ReplicatedStorage:FindFirstChild("Assets"),
    game.ReplicatedStorage:FindFirstChild("FX"),
}

-- Lọc bỏ các thư mục nil để tránh lỗi next()
local validFolders = {}
for _, folder in ipairs(v2) do
    if folder then
        table.insert(validFolders, folder)
    end
end

local v3 = nil
local u4 = nil
local u5 = nil

while true do
    local v6
    v3, v6 = v1(validFolders, v3)
    if v3 == nil then
        break
    end

    local v7 = next
    local children = v6:GetChildren()
    local v9 = nil

    while true do
        local v9_child, v10 = v7(children, v9)
        v9 = v9_child
        if v9 == nil then
            break
        end
        if v10:IsA('RemoteEvent') and v10:GetAttribute('Id') then
            u5 = v10:GetAttribute('Id')
            u4 = v10
        end
    end

    v6.ChildAdded:Connect(function(p11)
        if p11:IsA('RemoteEvent') and p11:GetAttribute('Id') then
            u5 = p11:GetAttribute('Id')
            u4 = p11
        end
    end)
end

-- ====================================================================
-- PHẦN 3: VÒNG LẶP FAST ATTACK TỐC ĐỘ CAO & THUẬT TOÁN MÃ HÓA XOR
-- (Đã vá lỗi null safety cho Character và Workspace folders)
-- ====================================================================
task.spawn(function()
    while task.wait(0.0001) do
        if _G.AutoAttack then
            local player = game.Players.LocalPlayer
            local _Character = player and player.Character
            local v13 = _Character and _Character:FindFirstChild('HumanoidRootPart')

            -- Đảm bảo nhân vật đã load đầy đủ trước khi quét
            if _Character and v13 then
                local enemiesFolder = workspace:FindFirstChild('Enemies')
                local charsFolder = workspace:FindFirstChild('Characters')
                
                local targetsList = {}
                if enemiesFolder then
                    table.insert(targetsList, enemiesFolder)
                end
                if charsFolder then
                    table.insert(targetsList, charsFolder)
                end

                local v14, v15, v16 = ipairs(targetsList)
                local u17 = {}

                while true do
                    local v18
                    v16, v18 = v14(v15, v16)
                    if v16 == nil then
                        break
                    end

                    local v19, v20, v21 = ipairs(v18 and v18:GetChildren() or {})

                    while true do
                        local v22
                        v21, v22 = v19(v20, v21)
                        if v21 == nil then
                            break
                        end

                        local _HumanoidRootPart = v22:FindFirstChild('HumanoidRootPart')
                        local _Humanoid = v22:FindFirstChild('Humanoid')

                        if v22 ~= _Character and (_HumanoidRootPart and (_Humanoid and (_Humanoid.Health > 0 and (_HumanoidRootPart.Position - v13.Position).Magnitude <= 80))) then
                            local v25, v26, v27 = ipairs(v22:GetChildren())

                            while true do
                                local v28
                                v27, v28 = v25(v26, v27)
                                if v27 == nil then
                                    break
                                end
                                if v28:IsA('BasePart') and (_HumanoidRootPart.Position - v13.Position).Magnitude <= 80 then
                                    u17[#u17 + 1] = {v22, v28}
                                end
                            end
                        end
                    end
                end

                local _Tool = _Character:FindFirstChildOfClass('Tool')

                if #u17 > 0 and (_Tool and (_Tool:GetAttribute('WeaponType') == 'Melee' or _Tool:GetAttribute('WeaponType') == 'Sword')) then
                    pcall(function()
                        local netModule = game.ReplicatedStorage:FindFirstChild('Modules') 
                        and game.ReplicatedStorage.Modules:FindFirstChild('Net')
                        
                        if netModule then
                            require(netModule):RemoteEvent('RegisterHit', true)
                            if netModule:FindFirstChild('RE/RegisterAttack') then
                                netModule['RE/RegisterAttack']:FireServer()
                            end

                            local _Head = u17[1][1]:FindFirstChild('Head')

                            if _Head and u4 and u5 then
                                if netModule:FindFirstChild('RE/RegisterHit') then
                                    netModule['RE/RegisterHit']:FireServer(_Head, u17, {}, tostring(player.UserId):sub(2, 4) .. tostring(coroutine.running()):sub(11, 15))
                                end
                                
                                local safeCloneRef = (typeof(cloneref) == "function") and cloneref(u4) or u4
                                local seedInvoke = (netModule:FindFirstChild('seed') and netModule.seed:InvokeServer()) or 1
                                
                                safeCloneRef:FireServer(string.gsub('RE/RegisterHit', '.', function(p31)
                                    return string.char(bit32.bxor(string.byte(p31), math.floor(workspace:GetServerTimeNow() / 10 % 10) + 1))
                                end), bit32.bxor(u5 + 909090, seedInvoke * 2), _Head, u17)
                            end
                        end
                    end)
                end
            end
        end
    end
end)-- Services
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer
local cloneref = cloneref or function(obj) return obj end

-- ====================================================================
-- TẠO UI (KHUNG CẦU VỒNG, NỀN đen, CHỮ Bee xanh, LEVEL & MONEY)
-- ====================================================================
local ExistingGui = CoreGui:FindFirstChild("DuboHubUI")
if ExistingGui then ExistingGui:Destroy() end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BeeHubUI"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 220, 0, 115)
MainFrame.Position = UDim2.new(0.05, 0, 0.1, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Nền XANH
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

-- Khung viền cầu vồng
local UIStroke = Instance.new("UIStroke")
UIStroke.Thickness = 3
UIStroke.Parent = MainFrame

local UIGradient = Instance.new("UIGradient")
UIGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 0, 0)),
    ColorSequenceKeypoint.new(0.2, Color3.fromRGB(255, 165, 0)),
    ColorSequenceKeypoint.new(0.4, Color3.fromRGB(255, 255, 0)),
    ColorSequenceKeypoint.new(0.6, Color3.fromRGB(0, 255, 0)),
    ColorSequenceKeypoint.new(0.8, Color3.fromRGB(0, 0, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(128, 0, 128))
})
UIGradient.Parent = UIStroke

task.spawn(function()
    while task.wait() do
        UIGradient.Rotation = (UIGradient.Rotation + 1) % 360
    end
end)

-- Tiêu đề Bee xanh
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, 0, 0, 35)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Font = Enum.Font.FredokaOne
TitleLabel.Text = "Bee hub"
TitleLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
TitleLabel.TextSize = 22
TitleLabel.Parent = MainFrame

-- Hiển thị Level & Money
local InfoLabel = Instance.new("TextLabel")
InfoLabel.Size = UDim2.new(1, -10, 0, 70)
InfoLabel.Position = UDim2.new(0, 5, 0, 38)
InfoLabel.BackgroundTransparency = 1
InfoLabel.Font = Enum.Font.GothamBold
InfoLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
InfoLabel.TextSize = 13
InfoLabel.TextXAlignment = Enum.TextXAlignment.Left
InfoLabel.TextYAlignment = Enum.TextYAlignment.Top
InfoLabel.TextWrapped = true
InfoLabel.Parent = MainFrame

task.spawn(function()
    while task.wait(0.5) do
        pcall(function()
            local data = LocalPlayer:FindFirstChild("Data")
            if data then
                local level = data:FindFirstChild("Level") and data.Level.Value or 1
                local beli = data:FindFirstChild("Beli") and data.Beli.Value or 0
                local frag = data:FindFirstChild("Fragments") and data.Fragments.Value or 0
                InfoLabel.Text = "• Level: " .. tostring(level) .. "\n• Beli: " .. tostring(beli) .. "\n• Fragments: " .. tostring(frag)
            end
        end)
    end
end)

-- ====================================================================
-- CÀI ĐẶT THÔNG SỐ & CÔNG TẮC
-- ====================================================================
_G.AutoFarm = true        -- Tự động di chuyển & nhận Quest
_G.AutoAttack = true      -- Tự động đánh quái tốc độ cao (Fast Attack)
_G.AutoHaki = true        -- Tự động bật Haki
_G.AutoEquipMelee = true  -- Tự động cầm Võ (Melee) khi farm
_G.AutoStatsMelee = true  -- Tự động cộng hết điểm vào Melee
_G.AutoRedeemCodes = true -- TỰ ĐỘNG REDEEM ALL CODES
_G.HeightOffset = 25      -- Độ cao Y nâng lên để bay trên đầu quái
_G.TweenSpeed = 200       -- Tốc độ bay (Tween)
_G.BringMobRadius = 250   -- Bán kính gom quái rộng

-- THÔNG SỐ CƠ CHẾ NHẢ - GOM
_G.BringInterval = 0.2    -- Thời gian giữ gom (giây)
_G.ReleaseInterval = 0.3  -- Thời gian nhả gom để Server tính Hitbox (giây)

local isYLocked = false
local lockedYValue = 0

-- ====================================================================
-- PHẦN TỰ ĐỘNG REDEEM CODE
-- ====================================================================
local CodeList = {
    "EASTEREXP",
    "SUB2GAMERROBOT_EXP1",
    "Sub2Fer999",
    "Sub2OfficialNoobie",
    "SUB2CAPTAINMAUI",
    "Enyu_is_Pro",
    "Starcodeheo",
    "Magicbus",
    "JCWK",
    "kittgaming",
    "Bluxxy",
    "Sub2Daigrock",
    "Axiore",
    "Sub2NoobMaster123",
    "StrawHatMaine",
    "TantaiGaming",
    "TheGreatAce"
}

task.spawn(function()
    repeat task.wait(0.5) until game:IsLoaded() and LocalPlayer and LocalPlayer:FindFirstChild("Data")
    if _G.AutoRedeemCodes then
        local redeemRemote = ReplicatedStorage:WaitForChild("Remotes", 10) and ReplicatedStorage.Remotes:WaitForChild("Redeem", 10)
        if redeemRemote then
            for _, code in ipairs(CodeList) do
                pcall(function()
                    local args = { [1] = code }
                    redeemRemote:InvokeServer(unpack(args))
                end)
                task.wait(0.5)
            end
        end
    end
end)

-- ====================================================================
-- BẢNG CẤU HÌNH FARM & ĐẢO & NHIỆM VỤ
-- ====================================================================
local QuestData = {
    ["Starter Island"] = {
        MinLevel = 1, MaxLevel = 9,
        Quest1 = {
            QuestName = "BanditQuest1", QuestLevel = 1, KillCount = 5,
            NPCName = "Bandit Quest Giver", MobName = "Bandit",
            NPCCFrame = CFrame.new(1052.41, 14.68, 1555.85),
            MobPositions = {CFrame.new(1001.81, 14.51, 1566.67), CFrame.new(928.30, 14.51, 1595.58)}
        }
    },
    ["Jungle"] = {
        MinLevel = 10, MaxLevel = 29,
        Quest1 = {
            QuestName = "JungleQuest", QuestLevel = 1, KillCount = 6,
            NPCName = "Adventurer", MobName = "Monkey",
            NPCCFrame = CFrame.new(-1680.91, 50.52, 174.86),
            MobPositions = {CFrame.new(-1527.27, 30.42, 163.39), CFrame.new(-1635.49, 30.42, 20.55)}
        },
        Quest2 = {
            QuestName = "JungleQuest", QuestLevel = 2, ReqLevel = 15, KillCount = 8,
            NPCName = "Adventurer", MobName = "Gorilla",
            NPCCFrame = CFrame.new(-1680.91, 50.52, 174.86),
            MobPositions = {CFrame.new(-1400.84, 26.62, -463.29), CFrame.new(-1270.48, 13.90, -488.81)}
        }
    },
    ["Pirate Village"] = {
        MinLevel = 30, MaxLevel = 59,
        Quest1 = {
            QuestName = "BuggyQuest1", QuestLevel = 1, KillCount = 8,
            NPCName = "Pirate Adventurer", MobName = "Pirate",
            NPCCFrame = CFrame.new(-1149.79, 17.29, 3862.69),
            MobPositions = {CFrame.new(-1123.29, 17.29, 3964.08), CFrame.new(-1176.44, 17.29, 4027.00)}
        },
        Quest2 = {
            QuestName = "BuggyQuest1", QuestLevel = 2, ReqLevel = 40, KillCount = 8,
            NPCName = "Pirate Adventurer", MobName = "Brute",
            NPCCFrame = CFrame.new(-1149.79, 17.29, 3862.69),
            MobPositions = {CFrame.new(-1089.10, 27.14, 4343.10), CFrame.new(-941.66, 27.14, 4387.25)}
        }
    },
    ["Snow"] = {
        MinLevel = 90, MaxLevel = 119,
        Quest1 = {
            QuestName = "SnowQuest", QuestLevel = 1, KillCount = 7,
            NPCName = "Villager", MobName = "Snow Bandit",
            NPCCFrame = CFrame.new(1400.19, 79.90, -1309.60),
            MobPositions = {CFrame.new(1260.42, 78.02, -1386.97), CFrame.new(1362.93, 78.02, -1429.17)}
        },
        Quest2 = {
            QuestName = "SnowQuest", QuestLevel = 2, ReqLevel = 100, KillCount = 8,
            NPCName = "Villager", MobName = "Snowman",
            NPCCFrame = CFrame.new(1400.19, 79.90, -1309.60),
            MobPositions = {CFrame.new(1247.04, 97.38, -1556.78), CFrame.new(1272.09, 97.38, -1645.68)}
        }
    },
    ["Marine Fortress"] = {
        MinLevel = 120, MaxLevel = 149,
        Quest1 = {
            QuestName = "MarineQuest2", QuestLevel = 1, KillCount = 8,
            NPCName = "Marine", MobName = "Chief Petty Officer",
            NPCCFrame = CFrame.new(-4699.39, 6.18, 4226.24),
            MobPositions = {CFrame.new(-4762.93, 14.21, 4287.91), CFrame.new(-4780.95, 14.21, 4419.18)}
        }
    },
    ["Ye"] = {
        MinLevel = 150, MaxLevel = 199,
        Quest1 = {
            QuestName = "SkyQuest", QuestLevel = 1, KillCount = 7,
            NPCName = "Sky Adventurer", MobName = "Sky Bandit",
            NPCCFrame = CFrame.new(-5405.79, 410.74, -695.36),
            MobPositions = {CFrame.new(-5157.47, 279.18, -916.99), CFrame.new(-5013.92, 279.18, -970.86)}
        },
        Quest2 = {
            QuestName = "SkyQuest", QuestLevel = 2, ReqLevel = 175, KillCount = 8,
            NPCName = "Sky Adventurer", MobName = "Dark Master",
            NPCCFrame = CFrame.new(-5405.79, 410.74, -695.36),
            MobPositions = {CFrame.new(-5269.72, 501.50, -461.03), CFrame.new(-5208.85, 501.50, -348.93)}
        }
    },
    ["Prison"] = {
        MinLevel = 200, MaxLevel = 249,
        Quest1 = {
            QuestName = "PrisonerQuest", QuestLevel = 1, KillCount = 8,
            NPCName = "Jail Keeper", MobName = "Prisoner",
            NPCCFrame = CFrame.new(5206.54, 19.55, 736.05),
            MobPositions = {CFrame.new(5457.48, 10.31, 507.84), CFrame.new(5371.72, 10.31, 458.79)}
        },
        Quest2 = {
            QuestName = "PrisonerQuest", QuestLevel = 2, ReqLevel = 210, KillCount = 8,
            NPCName = "Jail Keeper", MobName = "Dangerous Prisoner",
            NPCCFrame = CFrame.new(5206.54, 19.55, 736.05),
            MobPositions = {CFrame.new(5059.54, 10.31, 900.75), CFrame.new(5122.52, 10.31, 969.27)}
        }
    },
    ["Colosseum"] = {
        MinLevel = 250, MaxLevel = 299,
        Quest1 = {
            QuestName = "ColosseumQuest", QuestLevel = 1, KillCount = 7,
            NPCName = "Colosseum Quest Giver", MobName = "Toga Warrior",
            NPCCFrame = CFrame.new(-1342.11, 12.87, -2926.93),
            MobPositions = {CFrame.new(-1533.93, 12.20, -2779.14), CFrame.new(-1621.75, 12.20, -2672.02)}
        },
        Quest2 = {
            QuestName = "ColosseumQuest", QuestLevel = 2, ReqLevel = 275, KillCount = 8,
            NPCName = "Colosseum Quest Giver", MobName = "Mob Leader",
            NPCCFrame = CFrame.new(-1342.11, 12.87, -2926.93),
            MobPositions = {CFrame.new(-1163.32, 12.20, -3095.61), CFrame.new(-1120.11, 12.20, -3014.83)}
        }
    },
    ["Volcano"] = {
        MinLevel = 300, MaxLevel = 374,
        Quest1 = {
            QuestName = "MagmaQuest", QuestLevel = 1, KillCount = 7,
            NPCName = "The Mayor", MobName = "Military Soldier",
            NPCCFrame = CFrame.new(-5310.64, 18.46, 8482.92),
            MobPositions = {CFrame.new(-5419.00, 18.46, 8421.24), CFrame.new(-5620.39, 18.46, 8316.12)}
        },
        Quest2 = {
            QuestName = "MagmaQuest", QuestLevel = 2, ReqLevel = 325, KillCount = 8,
            NPCName = "The Mayor", MobName = "Military Spy",
            NPCCFrame = CFrame.new(-5310.64, 18.46, 8482.92),
            MobPositions = {CFrame.new(-5951.68, 77.31, 8741.88), CFrame.new(-5810.24, 77.31, 8790.60)}
        }
    },
    ["Underwater"] = {
        MinLevel = 375, MaxLevel = 449,
        Quest1 = {
            QuestName = "FishmanQuest", QuestLevel = 1, KillCount = 8,
            NPCName = "King Neptune", MobName = "Fishman Warrior",
            NPCCFrame = CFrame.new(61404.37, 25.86, 1626.76),
            MobPositions = {CFrame.new(60931.03, 24.93, 1395.48), CFrame.new(60789.84, 24.92, 1484.50)}
        },
        Quest2 = {
            QuestName = "FishmanQuest", QuestLevel = 2, ReqLevel = 400, KillCount = 7,
            NPCName = "King Neptune", MobName = "Fishman Commando",
            NPCCFrame = CFrame.new(61404.37, 25.86, 1626.76),
            MobPositions = {CFrame.new(61807.26, 24.93, 1328.87), CFrame.new(61887.79, 24.93, 1231.55)}
        }
    },
    ["Upper Sky"] = {
        MinLevel = 450, MaxLevel = 524,
        Quest1 = {
            QuestName = "SkyExp1Quest", QuestLevel = 1, KillCount = 7,
            NPCName = "Mole", MobName = "God’s Guard",
            NPCCFrame = CFrame.new(-4310.99, 1088.41, -556.90),
            MobPositions = {CFrame.new(-4225.30, 1087.12, -565.72), CFrame.new(-4085.80, 1087.12, -416.09)}
        },
        Quest2 = {
            QuestName = "SkyExp1Quest", QuestLevel = 2, ReqLevel = 475, KillCount = 9,
            NPCName = "Mole", MobName = "Shanda",
            NPCCFrame = CFrame.new(-5950.59, 5469.15, 2085.72),
            MobPositions = {CFrame.new(-6025.60, 5469.15, 1851.20), CFrame.new(-5891.98, 5469.15, 1956.11)}
        }
    },
    ["Upper Sky 2"] = {
        MinLevel = 525, MaxLevel = 624,
        Quest1 = {
            QuestName = "SkyExp2Quest", QuestLevel = 1, KillCount = 8,
            NPCName = "Sky Quest Giver 2", MobName = "Royal Squads",
            NPCCFrame = CFrame.new(-7031.71, 5592.21, 1352.71),
            MobPositions = {CFrame.new(-6869.90, 5552.69, 1320.06), CFrame.new(-6919.98, 5549.46, 1193.65)}
        },
        Quest2 = {
            QuestName = "SkyExp2Quest", QuestLevel = 2, ReqLevel = 550, KillCount = 8,
            NPCName = "Sky Quest Giver 2", MobName = "Royal Soldier",
            NPCCFrame = CFrame.new(-7031.71, 5592.21, 1352.71),
            MobPositions = {CFrame.new(-7137.98, 5540.58, 1054.46), CFrame.new(-7174.08, 5540.58, 949.89)}
        }
    },
    ["Fountain"] = {
        MinLevel = 625, MaxLevel = 699,
        Quest1 = {
            QuestName = "FountainQuest", QuestLevel = 1, KillCount = 8,
            NPCName = "Freezeburg Quest Giver", MobName = "Galley Pirate",
            NPCCFrame = CFrame.new(5262.31, 76.61, 4088.04),
            MobPositions = {CFrame.new(5326.12, 76.08, 4017.13), CFrame.new(5429.21, 76.08, 3955.06)}
        },
        Quest2 = {
            QuestName = "FountainQuest", QuestLevel = 2, ReqLevel = 650, KillCount = 9,
            NPCName = "Freezeburg Quest Giver", MobName = "Galley Captain",
            NPCCFrame = CFrame.new(5262.31, 76.61, 4088.04),
            MobPositions = {CFrame.new(5899.50, 76.08, 4809.37), CFrame.new(5782.89, 76.08, 4869.27)}
        }
    }
}

-- ====================================================================
-- PHẦN TỰ ĐỘNG CỘNG ĐIỂM MELEE
-- ====================================================================
task.spawn(function()
    while task.wait(0.5) do
        if _G.AutoStatsMelee then
            pcall(function()
                if LocalPlayer:FindFirstChild("Data") and LocalPlayer.Data:FindFirstChild("Points") then
                    local points = LocalPlayer.Data.Points.Value
                    if points and points > 0 then
                        ReplicatedStorage.Remotes.CommF_:InvokeServer("AddPoint", "Melee", points)
                    end
                end
            end)
        end
    end
end)

-- ====================================================================
-- FAST ATTACK
-- ====================================================================
local u4 = nil
local u5 = nil

task.spawn(function()
    local v1 = next
    local v2 = {
        ReplicatedStorage:FindFirstChild("Util"),
        ReplicatedStorage:FindFirstChild("Common"),
        ReplicatedStorage:FindFirstChild("Remotes"),
        ReplicatedStorage:FindFirstChild("Assets"),
        ReplicatedStorage:FindFirstChild("FX"),
    }
    local v3 = nil

    while true do
        local v6
        v3, v6 = v1(v2, v3)
        if v3 == nil then break end
        if v6 then
            local v7 = next
            local v8, v9 = v6:GetChildren()

            while true do
                local v9_child, v10 = v7(v8, v9)
                v9 = v9_child
                if v9 == nil then break end
                if v10:IsA('RemoteEvent') and v10:GetAttribute('Id') then
                    u5 = v10:GetAttribute('Id')
                    u4 = v10
                end
            end

            v6.ChildAdded:Connect(function(p11)
                if p11:IsA('RemoteEvent') and p11:GetAttribute('Id') then
                    u5 = p11:GetAttribute('Id')
                    u4 = p11
                end
            end)
        end
    end
end)

task.spawn(function()
    while task.wait(0.0001) do
        if _G.AutoAttack then
            pcall(function()
                local _Character = LocalPlayer.Character
                if not _Character then return end
                local v13 = _Character:FindFirstChild('HumanoidRootPart')
                if not v13 then return end

                local v14, v15, v16 = ipairs({
                    workspace:FindFirstChild("Enemies"),
                    workspace:FindFirstChild("Characters"),
                })
                local u17 = {}

                while true do
                    local v18
                    v16, v18 = v14(v15, v16)
                    if v16 == nil then break end

                    local v19, v20, v21 = ipairs(v18 and v18:GetChildren() or {})

                    while true do
                        local v22
                        v21, v22 = v19(v20, v21)
                        if v21 == nil then break end

                        local _HumanoidRootPart = v22:FindFirstChild('HumanoidRootPart')
                        local _Humanoid = v22:FindFirstChild('Humanoid')

                        if v22 ~= _Character and (_HumanoidRootPart and (_Humanoid and (_Humanoid.Health > 0 and (_HumanoidRootPart.Position - v13.Position).Magnitude <= 80))) then
                            local v25, v26, v27 = ipairs(v22:GetChildren())

                            while true do
                                local v28
                                v27, v28 = v25(v26, v27)
                                if v27 == nil then break end
                                if v28:IsA('BasePart') and (_HumanoidRootPart.Position - v13.Position).Magnitude <= 80 then
                                    u17[#u17 + 1] = {v22, v28}
                                end
                            end
                        end
                    end
                end

                local _Tool = _Character:FindFirstChildOfClass('Tool')

                if #u17 > 0 and (_Tool and (_Tool:GetAttribute('WeaponType') == 'Melee' or _Tool:GetAttribute('WeaponType') == 'Sword')) then
                    require(ReplicatedStorage.Modules.Net):RemoteEvent('RegisterHit', true)
                    ReplicatedStorage.Modules.Net['RE/RegisterAttack']:FireServer()

                    local _Head = u17[1][1]:FindFirstChild('Head')

                    if _Head and u4 and u5 then
                        ReplicatedStorage.Modules.Net['RE/RegisterHit']:FireServer(_Head, u17, {}, tostring(LocalPlayer.UserId):sub(2, 4) .. tostring(coroutine.running()):sub(11, 15))
                        
                        cloneref(u4):FireServer(string.gsub('RE/RegisterHit', '.', function(p31)
                            return string.char(bit32.bxor(string.byte(p31), math.floor(workspace:GetServerTimeNow() / 10 % 10) + 1))
                        end), bit32.bxor(u5 + 909090, ReplicatedStorage.Modules.Net.seed:InvokeServer() * 2), _Head, u17)
                    end
                end
            end)
        end
    end
end)

-- ====================================================================
-- BỔ TRỢ AUTO FARM & CHECK NHIỆM VỤ
-- ====================================================================
local function getActiveQuestName()
    local playerGui = LocalPlayer:FindFirstChild("PlayerGui")
    if playerGui then
        local main = playe
loadstring(game:HttpGet("https://raw.githubusercontent.com/realredz/BloxFruits/refs/heads/main/Source.lua"))(Settings)
  end
  })