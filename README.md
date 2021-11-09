# oneblockplus-course-task5

### 不是完整的项目，仅上传关键代码

### 问题5: 实现存证模块的功能，包括：创建存证；撤销存证。
1. 代码（详见 poe/lib.rs）：
```
 #[pallet::weight(0)]
        pub fn create_claim(
            origin: OriginFor<T>,
            claim: Vec<u8>
        ) -> DispatchResultWithPostInfo {
            let sender = ensure_signed(origin)?;
            ensure!(!Proofs::<T>::contains_key(&claim), Error::<T>::ProofAlreadyExist);

            Proofs::<T>::insert(
                &claim,
                (sender.clone(),frame_system::Pallet::<T>::block_number())
            );

            Self::deposit_event(Event::ClaimCreated(sender, claim));

            Ok(().into())

        }

        #[pallet::weight(0)]
        pub fn revoke_claim(
            origin: OriginFor<T>,
            claim: Vec<u8>
        ) -> DispatchResultWithPostInfo {
            let sender = ensure_signed(origin)?;
            let (owner, _) = Proofs::<T>::get(&claim).ok_or(Error::<T>::ClaimNotExist)?;
            ensure!(owner == sender, Error::<T>::NotClaimOwner);

            Proofs::<T>::remove(&claim);

            Self::deposit_event(Event::ClaimRevoked(sender, claim));
            Ok(().into())
        }
```

2. 运行的截图：



![1-1](https://user-images.githubusercontent.com/3039255/140954587-b453e892-f2d4-4e0b-bc6f-73a25826c972.png)

![1-2](https://user-images.githubusercontent.com/3039255/140954609-41eaf408-e2bc-4039-a146-26258f3a4549.png)

![1-3](https://user-images.githubusercontent.com/3039255/140954613-bc7fb11b-1a5e-4dd0-9bd2-3ca3d7ecb849.png)

![1-4](https://user-images.githubusercontent.com/3039255/140955269-63e0c273-f315-4b33-bfb4-22ba514770cc.png)


### 问题5:为存证模块添加新的功能，转移存证，接收两个参数，一个是内容的哈希值，另一个是存证的接收账户地址。
1. 代码（详见 poe/lib.rs）：

```
       #[pallet::weight(0)]
        pub fn translate_claim(
            dest: OriginFor<T>,
            claim: Vec<u8>
        ) -> DispatchResultWithPostInfo {
            let receiver = ensure_signed(dest)?;
            let (_, _) = Proofs::<T>::get(&claim).ok_or(Error::<T>::ClaimNotExist)?;

            Proofs::<T>::mutate(&claim, |d| match d {
                Some(data) => *data = (receiver.clone(),frame_system::Pallet::<T>::block_number()),
                _ => (),
            });

            Self::deposit_event(Event::ClaimTraslated(receiver, claim));
            Ok(().into())
        }
```
2. 运行的截图：
![2](https://user-images.githubusercontent.com/3039255/140955331-ee791312-9a05-44a8-b34c-60eee5001080.png)

![2-2](https://user-images.githubusercontent.com/3039255/140955358-c9ec90e5-5fa7-4c8c-a34a-f57aa794db11.png)




